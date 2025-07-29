# islandline-api
const express = require('express');
const app = express();
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);
const PORT = process.env.PORT || 3000;

// ⚠️ Le middleware stripe.raw est requis AVANT tout autre express.json()
app.post('/webhook', express.raw({ type: 'application/json' }), (req, res) => {
  const sig = req.headers['stripe-signature'];
  let event;

  try {
    event = stripe.webhooks.constructEvent(req.body, sig, process.env.STRIPE_WEBHOOK_SECRET);
  } catch (err) {
    console.error(`❌ Signature invalide : ${err.message}`);
    return res.status(400).send(`Webhook Error: ${err.message}`);
  }

  // ✅ Traitement de l’événement
  if (event.type === 'checkout.session.completed') {
    const session = event.data.object;
    console.log('🎉 Paiement réussi ! Email :', session.customer_email);
    // Tu pourras ici envoyer l'info vers Roblox via HTTP, webhook, etc.
  }

  res.status(200).send('✅ Webhook reçu');
});

// Juste pour vérifier que le serveur tourne
app.get('/', (req, res) => {
  res.send('✅ Serveur IslandLine en ligne !');
});

app.listen(PORT, () => {
  console.log(`🔧 Serveur actif sur http://localhost:${PORT}`);
});
