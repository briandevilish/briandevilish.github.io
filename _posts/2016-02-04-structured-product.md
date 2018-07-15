---
title: Decomposing structured product
layout: default
---

{{ page.title }}
================
###ELN
- An investor who wants to have a capital protected investment with exposure in equity.
- 2 year to maturity ELN issued at 98% of notional linked with HSBC stock
  - Redemption At Maturity
    - 

###Autocallable
- Buyer of the Autocallable has range bound view on the stock, hence invested in Autocallable
- 2 Years Autocallable Note on Sony with the following features
  - Coupon
    - Paid and determined on a semi-annually basis
    - if Sony > 90%, pays 5%
    - if Sony <= 90%, pays nothing
  - Early Redemption
    - determined on a semi-annually basis
    - if Sony > 120%, note is redeems early and buyer will receive full investment back
  - Redemption At Maturity
    - determined on a daily basis close of market
    - if Sony has never drop below 70%, note redeems at 100%
    - if Sony has dropped below 70% during the note tenor, note redeems at 100% - max [(Sony at 0 - Sony at T)/Sony at 0, 0]
- To price this product, 
  - trader sell
    - 1 85/90% digital call expired on 1st semi-annual 
    - 1 85/90% digital call expired on 2nd semi-annual with knock-out at 120% determined at 1st semi-annual
    - 1 85/90% digital call expired on 3rd semi-annual with knock-out at 120% determined at 1st and 2nd semi-annual
    - 1 85/90% digital call expired on 4th semi-annual with knock-out at 120% determined at 1st, 2nd and 3rd semi-annual
    - 1 zero strike put with knock-out at 120% determined at 1st, 2nd, 3rd semi-annual and 0% knock-out at final period. Whenever it knock out, it pays 100%.
  - trader buy 
    - 1 100% put with knock-in at 70% and knock-out at 120% determined at 1st, 2nd and 3rd semi-annual

###Accumulator
- Typically invested by investor who has high-risk appetite and have an upward/downward view of the market.
- An investor who buys Accumulator are committed to buy x number of stock at a pre-determined price 
  called, Strike Price, on a specified observation days. It is typical for the contract to include a Knock Out
  Barrier (KO) which if on any Observation days the closing stock price above the KO price, the contract
  ends automatically. KO Price similarly with Strike Price are decided on Trade Date when Investor purchase
  the contract.
- Other option in Accumulator includes Guarantee Period and Leverage.
- Guarantee Period is the period in which KO will be not be effective.
- Leverage is when it is below the Strike Price, investor will buy n leverage shares.
- Accu is basically an issuer long forward contract on a daily basis in which the investor pays at Strike Price.
- Accu (without Leverage) can be structured by Issuer with One Long Call and One Short Put.
- As issuer own one Long Call at Strike Price, 
  - when Final Spot > Strike Price, Issuer can buy stock at Strike Price and purchased stock is delivered to Accu's investor
  - when Final Spot < Strike Price, Issuer call option expired worthless
- An issuer own one Short Put at Strike Price,
  - when Final Spot > Strike Price, Issuer put option will expired worthless
  - when Final Spot < Strike Price, Issuer will be sold stock at Strike Price which is then delivered to Accu's investor.
- For Accu with Leverage it is basically increase the Short Put at Strike Price by the leverage.
- ITM Call cost more than OTM Put. E.g., HSBC spot=55.20, Pricing of call expiring in 1M, call(ITM) with Strike=53 is 2.4, 
  while put(OTM) with Strike=53 is 0.3. Hence, the Issuer of Accumulator pays the Investor premium.
- Use https://www.hkex.com.hk/eng/sorc/tools/calculator_stock_option.aspx to calculate option price.
- Decumulator is the opposite of Accumulator, in which, the investor are committed to sell x number of stock at a
  pre-determined price on a specified observation days.
- Decu (without Leverage) can be structured by Issuer with One Long Put and One Short Call.
- An issuer own one Long Put at Strike Price,
  - when Final Spot > Strike Price, Issuer put option expired worthless
  - when Final Spot < Strike Price, Issuer sell one stock at Strike Price (Issuer receives the stock from Decu's investor)
- An issuer own one Short Call at Strike Price,
  - when Final Spot > Strike Price, Issuer must deliver one stock at Strike Price (Issuer receives the stock from Decu's investor)
  - when Final Spot < Strike Price, Issuer call option expired worthless





























