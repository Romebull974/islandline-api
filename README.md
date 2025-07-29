# islandline-api
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());

app.get('/', (req, res) => {
  res.send('Bienvenue sur Island Line API !');
});

app.post('/webhook', express.raw({ type: 'application/json' }), (req, res) => {
  const payload = req.body;
  console.log('📦 Webhook reçu :', payload);
  res.status(200).send('OK');
});

app.listen(PORT, () => {
  console.log(`✅ Serveur démarré sur le port ${PORT}`);
});
