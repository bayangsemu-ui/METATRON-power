# Panduan Instalasi METATRON-power

## Prasyarat Sistem

- **OS**: Parrot OS, Kali Linux, atau Linux lainnya
- **Python**: 3.8+
- **RAM**: Minimal 4GB (untuk model LLM lokal)
- **Storage**: 10GB+ untuk model LLM
- **Internet**: Diperlukan untuk download model pertama kali

## Langkah 1: Install Dependensi Sistem

```bash
sudo apt update
sudo apt install -y python3 python3-pip python3-venv git curl wget nmap
```

## Langkah 2: Install Ollama (Local LLM Runtime)

Ollama memungkinkan menjalankan model LLM lokal tanpa GPU mahal.

```bash
# Download dan install Ollama
curl -fsSL https://ollama.ai/install.sh | sh

# Verify instalasi
ollama --version

# Jalankan Ollama di background
ollama serve &
```

## Langkah 3: Download Model LLM

Pilih salah satu model berikut sesuai kapasitas RAM:

### Opsi A: Llama 3.1 (Recommended - 4.7GB)
```bash
ollama pull llama3.1
```

### Opsi B: Mistral (Lebih ringan - 4GB)
```bash
ollama pull mistral
```

### Opsi C: Neural Chat (Untuk pentesting - 4GB)
```bash
ollama pull neural-chat
```

**Cek model yang sudah terinstall:**
```bash
ollama list
```

## Langkah 4: Setup Project METATRON-power

```bash
# Clone repository
git clone https://github.com/bayangsemu-ui/METATRON-power.git
cd METATRON-power

# Buat virtual environment
python3 -m venv venv
source venv/bin/activate

# Install Python dependencies
pip install --upgrade pip
pip install -r requirements.txt
```

## Langkah 5: Konfigurasi Environment

Buat file `.env` di root project:

```bash
cat > .env << 'EOF'
# Flask/API Configuration
FLASK_ENV=development
FLASK_DEBUG=True
SECRET_KEY=your-secret-key-change-this-in-production
HOST=127.0.0.1
PORT=5000

# Ollama Configuration
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=llama3.1

# Database
DATABASE_URL=sqlite:///metatron.db

# JWT Authentication
JWT_SECRET=your-jwt-secret-key-change-this
JWT_EXPIRATION=86400

# Security Targets
ALLOWED_TARGETS_FILE=allowed_targets.json

# Logging
LOG_LEVEL=INFO
EOF
```

## Langkah 6: Inisialisasi Database

```bash
python3
>>> from app import db, app
>>> with app.app_context():
>>>     db.create_all()
>>> exit()
```

## Langkah 7: Jalankan METATRON-power

**Terminal 1 - Jalankan Ollama:**
```bash
ollama serve
```

**Terminal 2 - Jalankan METATRON API:**
```bash
cd METATRON-power
source venv/bin/activate
python3 app.py
```

**Terminal 3 - Jalankan Frontend (Optional):**
```bash
cd METATRON-power/frontend
npm install
npm start
```

## Akses Aplikasi

- **API**: http://localhost:5000
- **Web UI**: http://localhost:3000 (jika menggunakan frontend)
- **API Docs**: http://localhost:5000/api/docs

---

## Troubleshooting

### Ollama tidak terkoneksi
```bash
# Pastikan Ollama running
ollama serve

# Test koneksi
curl http://localhost:11434/api/tags
```

### Port 5000 sudah terpakai
```bash
# Ubah PORT di .env atau:
python3 app.py --port 5001
```

### Model LLM error/timeout
```bash
# Kurangi model size, gunakan Mistral
ollama pull mistral

# Update di .env:
OLLAMA_MODEL=mistral
```

### Permission denied untuk Nmap
```bash
# Tambah sudo privileges untuk nmap
sudo visudo
# Tambahkan baris:
# username ALL=(ALL) NOPASSWD: /usr/bin/nmap
```

---

## Verifikasi Instalasi

Jalankan script test:

```bash
python3 tests/test_setup.py
```

Output yang diharapkan:
```
✓ Python environment OK
✓ Ollama service running
✓ Model loaded: llama3.1
✓ Database initialized
✓ Nmap available
```

---

## Instalasi Tambahan (Optional)

### Install Nessus (Premium)
```bash
# Download dari https://www.tenable.com/products/nessus
# Follow instruksi untuk Parrot OS
```

### Install Metasploit Framework
```bash
sudo apt install -y metasploit-framework
msfconsole -h
```

### Install Burp Suite
```bash
# Download dari https://portswigger.net/burp/communitydownload
chmod +x burpsuite_community_*.sh
./burpsuite_community_*.sh
```

---

## Next Steps

1. Baca [PENGGUNAAN.md](PENGGUNAAN.md) untuk cara menggunakan sistem chat
2. Lihat [API_REFERENCE.md](API_REFERENCE.md) untuk dokumentasi API lengkap
3. Setup [allowed_targets.json](docs/allowed_targets_example.json) untuk target yang diizinkan

