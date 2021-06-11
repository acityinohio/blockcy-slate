# Updates

This section list all the updates in reverse chronological order. All dates are in the DD/MM/YY format.

**11/06/21 - Data Endpoint Terminated**
The data endpoint no longer exists as it was uneconomical and thus non functional since a long time.

**19/02/21 - Node-Client Archived**

BlockCypher Node.js [SDK](https://github.com/blockcypher/node-client) is now archived. We recommend that javascript users use HTTP libraries such as Axios. The [POSTMAN documentation](https://documenter.getpostman.com/view/12131330/TVCZYVRa#e2ffad48-d580-4442-8e77-2613dc16a525) contains all the examples that you need.

**18/02/21 - Virtual Size for Blocks**

Virtual size (`vsize`) for blocks.

**12/02/21 - Virtual Size for Segwit Transaction**

We've added the virtual size (`vsize`) for segwit transaction. For regular (non-segwit) transaction the `vsize` is the same as the `size`.
This is how you can see how much fees you are saving by using segwit. Enjoy!

**04/02/21 - Creation of the "Updates" Section**

We've created this new "Updates" section to let you know about the new feature and bugfixes we deployed :).

**03/02/21 - Additional DNS Seeds for Doge**

Following last week Dogecoin chain lag due to unhealthy network conditions, we've added new DNS seeds thanks to [Denarius](https://github.com/dogecoin/dogecoin/pull/1669).

**02/02/21 - Additional Support for Litecoin `Maddrs`**

Following previous updates to enhance the support of "Maddrs" on Litecoin, we've added several bugfixes:

- Correctly display "Maddrs" webhooks with standard wallet.
- Correctly display wallet webhooks with "Maddrs".
- Fix an error where a webhook will not be delivered if created with a standard wallet and a "Maddr". If you encounter this issue please recreate your wallet.
- In standard wallet, listing all addresses will correctly convert "3addrs" to "Maddrs".
- In standard wallet, getting a wallet will always show "Maddrs".

**27/11/20 - New DASH DNS Seeds**

We've updated our DASH DNS seeds in order to provide a more reliable service.

**23/11/20 - Payment Forward for Litecoin MAddrs**

We've added the ability to create payment forward to Litecoin address with the M prefix.

**17/11/20 - `OP_RETURN` Simplification**

We've added the ability to create `OP_RETURN` output as easily as including `null-data` as script-type. See the `TXOutput` object for more details.