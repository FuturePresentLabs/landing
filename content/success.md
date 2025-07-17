---
title: "Thank You for Your Purchase!"
type: "page"
---

# 🎉 Success!

Thank you for your order. We’ve received your payment and will process your item shortly.

<div id="order-id">
</div>

<script>
const params = new URLSearchParams(window.location.search);
const orderId = params.get("order-id");

if (orderId) {
document.getElementById("order-id").innerText =
`Your order ID is ${orderId}. We'll be in touch soon.`;
}
</script>

{{< contact body="If you have any questions or need support, please email us." header="Need help?">}}
