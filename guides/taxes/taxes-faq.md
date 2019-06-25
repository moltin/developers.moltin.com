# Taxes FAQ

{% hint style="danger" %}
Content moved to [Moltin Developer Blog](https://www.moltin.com/blog/february-2019-ama-working-with-taxes).
{% endhint %}

## Taxes in general

**Q:** **How can I make sure I calculate taxes correctly?**

**A:** By using a paid third party which offers guarantees. At the moment, Moltin provides seamless integration with a third party taxes calculator, TaxJar.

**Q: Who calculates taxes for me? Does Moltin calculate taxes?**

**A**: No, the actual calculation is handled outside Moltin by a third party like Taxjar who devote their entire business to doing it well.

**Q: Are you going to build your own tax calculator in the future?**

**A**: We tend to take the philosophy that if there is an entire business elsewhere devoted to doing this thing well, we will make it super easy to integrate with them while we focus on what is core to Moltin, so in this case we do not plan to build our own tax calculator.

**Q: Is nexus I use validated for me? How can I make sure the nexus I use is correct?**

**A**: TaxJar will do [nexus validation](https://support.taxjar.com/article/115-how-do-i-know-where-i-have-nexus/%20) for you.

## Cost of taxes implementation

**Q: How much is this integration going to cost me overall?**

**A**: This depends on the number of transactions you have, and which third party provide you use. We recommend comparing taxjar and avalara.

**Q: Is the Moltin part of this functionality free?**

**A**: Yes, this functionality is provided as part of Moltin core features. How much is for your monthly fee, depends on your current plan outlined on our new [pricing page](https://moltin.com/headless-commerce-platform/pricing/)

**Q: How much does TaxJar cost?**

**A**: Please refer to [TaxJar's pricing page](https://www.taxjar.com/pricing/plans/). 

## How taxes work with Moltin

**Q: I have my own tax calculator, can I use it with Moltin?**

**A**: Absolutely! Just call it at the point your application is ready to calculate, then add the resulting amounts to Moltin as normal. Here’s some helpful resources on our [Developer Portal](https://developers.moltin.com/guides/taxes).

**Q: Is there any app example I could use and adapt to my use case?**

**A**: Absolutely, we have built a simple demo site in React that you can find on [GitHub](https://github.com/moltin/taxjar-example).

**Q: Are taxes supported for currency other than USD?**

**A**: Yes, taxes will be applied in the same currency as your cart.

**Q: Are taxes applied only for default currency?**

**A**: No, it is dependant on the currency used in the Moltin cart they are being applied to.

**Q: What if the user changes their shipping address after the sale?**

A: Because Moltin doesn't calculate the tax, a new order with an updated shipping address will need to be created by checking the product out again.

**Q: Is tax applied to the entire cart or to each individual cart item?**

**A**: Each individual cart item. Additionally, there’s a certain chronology to how you apply these for each cart item. For example, if having a promotion applied to this item, the tax is added after the appropriate discount has been calculated.

**Q: All I need is flat tax rate on the cart overall instead of on each item, how would that work with Moltin?**

**A**: In case of a flat rate, you could add a custom cart item with the tax, but please be aware, this will not affect the `with_tax` and `without_tax`. If you want to have an audit trail, you should distribute the flat rate across the items in the cart by adding a tax item for each product. Take the product price, and calculate the rate that will result in the flat rate you need.

**Q: What if I sell a discounted product? Would the tax be applied after or before the promotion is applied to the product?**

**A**: Taxes are calculated after all promotional savings have been applied.

**Q: Can I exempt a customer from taxes using Moltin?**

**A**: Yes, you can. If you don’t want to apply a tax to any product, disable the `include_tax` flag for that product.

**Q: Can I override a particular tax code?**

**A**: When you make the call to the tax provider, you can send whatever tax code you like, even calculating dynamically when you need to

**Q: Where can I see what tax and jurisdiction were calculated for my invoice?**

**A**: The tax values will be visible on your Moltin order.

**Q: Can I apply taxes from the dashboard?**

**A**: No you cannot.

