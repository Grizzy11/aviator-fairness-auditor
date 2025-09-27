# ✈️ Aviator Provably Fair Auditor

A **Streamlit web app** that verifies Aviator crash game fairness by auditing server seeds, client seeds, and published hashes.  
It checks the integrity of each round and calculates the crash point multiplier using provably fair algorithms.

---

## 🚀 Features

- ✅ **Server Seed Verification** – Confirms if the provided server seed matches the published SHA256 hash.  
- 🎲 **Crash Point Calculation** – Uses server + client seeds with SHA512 to compute the crash multiplier.  
- 📂 **CSV Upload Support** – Upload multiple rounds in one file.  
- 🔍 **Check-only Mode** – Verify seeds without calculating crash multipliers.  
- 📊 **Results Viewer** – Displays results in a clean Streamlit table.  
- 💾 **Export Results** – Download verification results as **CSV** or **JSON**.  

---

## 📂 CSV Format

Your input CSV must contain these columns:

| round_id | published_hash | server_seed | client1 | client2 | client3 |
|----------|----------------|-------------|---------|---------|---------|
| 1        | 4b227777d4dd1f... | secretseed123 | abc     | def     | ghi     |

- **published_hash** → SHA256 hash of the server seed.  
- **server_seed** → The raw server seed.  
- **client1, client2, client3** → Client seeds merged with server seed.  
- **round_id** *(optional)* → Identifier for the round.  

---

## 🛠️ Installation

Clone this repository:

```bash
git clone https://github.com/yourusername/aviator-auditor.git
cd aviator-auditor
