# Fee Auctions

> ℹ️ Fees received by the protocol are currently held in a module account and in the v7 upgrade will be auctioned for SOMM, with the proceeds being distributed pro rata to stakers of SOMM. This document overviews how the module works and how to participate in auctions. 

## Overview

Periodically, cellars bridge accumulated performance and/or management fees of various denominations, such as USDC, wETH, wBTC, etc., to a `cellarfees` module account `somm1hqf42j6zxfnth4xpdse05wpnjjrgc864vwujxx` on the Sommelier chain. In order to convert these fees to SOMM which can be distributed to SOMM stakers, the protocol hosts **dutch auctions** in which arbitrageurs have the opportunity to purchase bridged fees at a discount using SOMM. They may then bridge the fees back to their native chain and swap them for a profit.

A **dutch auction** is one in which the ask price of the asset decreases over time, and bids are fulfilled on a first-come-first-serve basis. This differs from a regular auction where the auctioneer would fulfill orders based on the most favorable bid. Instead, bidders may execute the exchange whenever they see fit, meaning a bidder must choose between waiting for a better discount and buying before other bidders buy out the auction.

There will be one auction per fee asset held at a minimum block interval set by governance. The denomination's starting price in terms of SOMM is also determined by a governance proposal and should be updated regularly. This implies that the starting price will almost always start at an unfavorable price for bidders. Once an auction starts, the ask price will decrease periodically, and the rate of price decrease will accelerate when there is low participation to encourage bidding. Bidders must monitor active auctions, bids, and bridging and gas fees carefully if they want to win the fees at a profit. 

An auction ends at either its pre-determined ending block height, or when the fee supply has been exhausted by bidders. If an auction ends and there is supply leftover, it is returned to the `cellarfees` module to auctioned later. The SOMM received from bidders is distributed to stakers.

## Auction Participation

Protobufs for the Sommelier chain define the API of the `auction` module. They can be found [here](https://github.com/PeggyJV/sommelier/tree/main/proto/auction/v1). 

Here we will overview the core types involved in an auctions (`Auction` and `Bid`), how to query them, and how to submit bids. Generating proto bindings in a desired programming language is left as an exercise for the user. This document will use pseudocode where code examples are needed.

## Auctions

The [`Auction`](https://github.com/PeggyJV/sommelier/blob/bd0a767f599e6665e8a059e5db0ca6492eec07ec/proto/auction/v1/auction.proto#L10-L35) message is the essential representation of a fee auction. It contains important information for a bidder, such as:

- `id`: The unique ID number of the auction. This can be used to query information about an auction its bids.
- `starting_tokens_for_sale`: The denomination and amount of the fee available at the *beginning of the auction* represented as a `cosmos.base.v1beta1.Coin`.
- `remaining_tokens_for_sale`: The denomination and amount of the fee still available for purchase represented as a `cosmos.base.v1beta1.Coin`.
- `end_block`: The Sommelier block height at which the auction will end regardless of remaining tokens.
- `initial_unit_price_in_usomm`: The governance-determined starting price of the auction's fee denomination **in uSOMM**. 
- `current_unit_price_in_usomm`: The current unit price of the auction's fee denomination **in uSOMM**.
 
> ℹ️    uSOMM is one millionth of 1 SOMM (0.000001 SOMM)

* `price_decrease_block_interval`: How frequently the ask price will decrease. This can be used with `start_block` to calculate the block heights at which price decreases will occur. 
- `initial_price_decrease_rate`: The starting magnitude of price decrease per interval as defined by `price_decrease_block_interval`.
- `current_price_decrease_rate`: The current magnitude of price decrease per interval as defined by `price_decrease_block_interval`.

### Querying auctions

Both active and ended auctions are available for querying. 

`QueryActiveAuctions` will return a list of all `Auction` that are live and thus able to accept bid submission requests. 

`QueryActiveAuction` (no plural) takes an auction ID number and returns an `Auction` if it is indeed active.

Querying ended auctions works the same way, simply use the `QueryEndedAuctions` and `QueryEndedAuction` queries respectively.

## Bids

A [`Bid`](https://github.com/PeggyJV/sommelier/blob/bd0a767f599e6665e8a059e5db0ca6492eec07ec/proto/auction/v1/auction.proto#L37-L52)  represents a bidder's attempt to purchase fee tokens from a particular auction. It contains information such as the maximum amount of SOMM the bidder was willing to pay, the minimum units of fee tokens they wanted to receive in return, and how many units were actually purchased based on these parameters and the  price in uSOMM at the executed block height. A bid's purchase is settled in the same block in which the submitted bid transaction is included. There are no "limit order" bids in Sommelier auctions.

The amount of fees purchased is determined by how many tokens can be purchased with the bid's `max_bid_in_usomm` given that there are greater than `sale_token_minimum_amount` tokens still available in the auction. Bids are settled in the order in which they are received. This means that not all bids that are submitted are guaranteed to be successful.  
### Querying Bids

Users may want to query bids for active or past auctions for a number of reasons, including seeing the settlement of their own bids, or the bidding behavior of other participants.

Bids can be queried using `QueryBid` or `QueryBidsByAuction`. Both queries return bids for one specific auction indicated by an auction ID in the respective request.

`QueryBid` can be used to look up the settlement of ones own bid request using a bid ID and auction ID. The bid ID is included in the response to a bid submission request. It returns a `Bid`.

`QueryBidsByAuction` returns all `Bid` for the provided auction ID.

### Submitting a Bid

To submit a bid, you must call `SubmitBid` using your message client, passing in a [MsgSubmitBidRequest](https://github.com/PeggyJV/sommelier/blob/bd0a767f599e6665e8a059e5db0ca6492eec07ec/proto/auction/v1/tx.proto#L15-L20). The `max_bid_in_usomm`, `sale_token_minimum_amount`, along with the current price and remaining tokens for sale of the auction identified by `auction_id` are what determine the sale price and final amount purchased.

The following is a pseudocode example. The bidder's intent is to check if a particular token is up for auction and to submit a bid if it is and there are enough units still available for purchase.

```pseudocode
import auction_proto
import cosmos_proto

// The gRPC endpoint of a Sommelier validator for querying and 
// submitting transactions.
grpc_endpoint = "https://sommelier-grpc.example.com";

// The address of the wallet that will sign any submitted bid
// transactions
my_address = "somm10000000000000000000000000000000example"

// Example denom of a token bridged from Ethereum. The address is
// the string 'gravity' concatinated with the lowercase ERC20 
// Ethereum address.
want_denom = "gravity0x0000000000000000000000000000000000000000"
want_amount = 1000000

// Construct a query client from the proto bindings in your language
// of choice.
query_client = new auction_proto.QueryClient(grpc_endpoint);

// Check if there is an active auction for our target denom and that
// there are enough left to purchase for our purposes.
response = query_client.active_auctions()
active_auctions = response.auctions

for aa in active_auctions {
	for_sale = aa.remaining_tokens_for_sale
		
	if for_sale.denom == want_denom {
        if for_sale.amount < want_amount {
            // In this example we're only interested in one particular
            // token. Since there can only be one active auction for a
            // given token, we return.
            print("we shouldn't bid")
            return
        }

		// Submit the bid
		msg_client = new auction_proto.MsgClient(grpc_endpoint)

		request = new auction_proto.MsgSubmitBidRequest {
			auction_id: aa.id,
			signer: my_address,
			max_bid_in_usomm: new cosmos_proto.Coin {
				denom: "usomm",
				amount: 12000,
			},
			sale_token_minimum_amount: new cosmos_proto.Coin {
				denom: want_denom,
				amount: want_amount,
			},
		}
		
		response = msg_client.submit_bid(request)

        // Print the settled bid
		bid = response.bid
		
		print(
			"minimum of %v tokens were wanted and of %v were purchased at a price of %v uSOMM", 
			want_amount,
			bid.total_fulfilled_sale_tokens.amount,
			bid.total_usomm_paid,
		)
	} 
}
```

### Example Auction Story

An auction has started for 10,000 units of TokenA. The most recent **Token Prices** of TokenA and SOMM submitted by governance are the 52 week high of TokenA at \$1.00 per unit and 52 week low of SOMM at \$0.10. Because of price of SOMM is above \$0.09 at the time of the auction, any bids that purchase TokenA too early in the auction will do so at a loss.

Bidder A wants to take as little risk as possible, and that means not holding SOMM for too long to minimize price exposure as well as making sure their bid is relatively early in the auction so they don't get outbid. Bidder A has decided they will be happy with a 2% profit on the 10,000 bridged TokenA that is being auctioned. They decide that they will wait to buy the SOMM on Osmosis until the auction price is one update away from the price that will fulfill their target profit per unit.

Bidder B has a similar strategy to Bidder A, also targetting a profit of 2%. However, Bidder B has taken the risk of holding SOMM earlier so that they can bid quickly.

Neither Bidder A nor B remember to account for a bridge fee or the Ethereum gas price in their profit calculations.

For simplicity, we will assume the market price of SOMM and TokenA remains constant, and that the price of TokenA at the time of the auction is $1. In reality, the bidders would need to monitor these prices in real time for accurate profit estimates.

|Block Height|Minimum Price of TokenA in SOMM|Market Price of SOMM|Cost basis of SOMM|Market Price of TokenA in USD|Estimated Profit per Unit|Estimated % Return|Commentary|
|---|---|---|---|---|---|---|---|
|100000000|10|	\$0.2000|\$2.0000|\$1.0000|-\$1.0000|-50.00%|Auctions intentionally start overpriced in terms of SOMM. Any bids submitted too early will buy at a loss and effectively donate SOMM to stakers.|
|100000010|9.98|	\$0.2000|\$1.9960|\$1.0000|-\$0.9960|-49.90%||
|100000020|9.96|	\$0.2000|\$1.9920|\$1.0000|-\$0.9920|-49.80%||
|100000030|9.94|	\$0.2000|\$1.9880|\$1.0000|-\$0.9880|-49.70%||
|...|	...|	...|	...|	...|	...|	...|Time passes. Because no one places bids, the rate at which the quote price in SOMM decreases accelerates until bids occur.|
|100000530|4.94|	\$0.2000|\$0.9880|\$1.0000|\$0.0120|1.21%||
|100000540|4.92|	\$0.2000|\$0.9840|\$1.0000|\$0.0160|1.63%|Based on the rate of change observed, Bidder A predicts that the next price change will reach their desired profit per unit of 2%. They purchase SOMM from Osmosis and transfer it to the Sommelier chain to prepare to bid.|
|100000550|4.9|	\$0.2000|\$0.9800|\$1.0000|\$0.0200|2.04%|The price updates before Bidder A finishes transferring SOMM to the Sommelier chain, meanwhile Bidder B already has SOMM in their Sommelier wallet and bids enough SOMM to receive 6000 of TokenA, leaving 4000 for Bidder A.|


Both bidders bridge their TokenA to Ethereum, each paying 10 SOMM for the bridge fee and another $12 in gas fees when they close the arbitrage by swapping TokenA for USDC. The profit comparisons are shown here:

Additionally, Bidder A has leftover SOMM that they will sell because they purchased enough to buy 10,000 units of TokenA but only 4000 were available.

|Bidder|Expected Profit|Actual Profit|
|---|---|---|
|Bidder A|2.04% of \$9800 (\$200)|\$66|
|Bidder B|2.04% (\~\$120)|\$106|

#### Observations

- It is important to account for the cost of closing the arbitrage when calculating expected return. 
- Bidder A had assumed they'd get all 10,000 units of Token A, and the opportunity cost of being slow was significant. Because the process of participating is not an atomic transaction (SOMM must be purchased in an earlier transaction), bidders must balance exposure to SOMM price risk vs the opportunity cost of being outbid by a faster bidder. 

