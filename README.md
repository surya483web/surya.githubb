const express = require('express');
const bodyParser = require('body-parser');
const fs = require('fs').promises;

const app = express();
app.use(bodyParser.json());

// Receive location
app.post('/location', async (req, res) => {
  try {
    const data = req.body;
    data.receivedAt = new Date().toISOString();

    // Save to a file (locations.json)
    let log = [];
    try {
      const existing = await fs.readFile('locations.json', 'utf8');
      log = JSON.parse(existing);
    } catch {}
    log.push(data);
    await fs.writeFile('locations.json', JSON.stringify(log, null, 2));

    res.json({status: 'ok', saved: true});
  } catch (err) {
    res.status(500).json({status: 'error', message: err.message});
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
