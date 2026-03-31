# -renci-not-takip-sistemiimport sqlite3

# Veritabanı bağlantısı
conn = sqlite3.connect("ogrenciler.db")
cursor = conn.cursor()

# Tablo oluştur
cursor.execute("""
CREATE TABLE IF NOT EXISTS ogrenciler (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    isim TEXT NOT NULL
)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS notlar (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    ogrenci_id INTEGER,
    ders TEXT,
    not_degeri REAL,
    FOREIGN KEY (ogrenci_id) REFERENCES ogrenciler(id)
)
""")

conn.commit()

# Fonksiyonlar
def ogrenci_ekle(isim):
    cursor.execute("INSERT INTO ogrenciler (isim) VALUES (?)", (isim,))
    conn.commit()

def not_ekle(ogrenci_id, ders, notu):
    cursor.execute("INSERT INTO notlar (ogrenci_id, ders, not_degeri) VALUES (?, ?, ?)", (ogrenci_id, ders, notu))
    conn.commit()

def notlari_goster():
    cursor.execute("""
    SELECT ogrenciler.isim, notlar.ders, notlar.not_degeri
    FROM notlar
    JOIN ogrenciler ON notlar.ogrenci_id = ogrenciler.id
    """)
    for row in cursor.fetchall():
        print(row)

def ortalama_hesapla(ogrenci_id):
    cursor.execute("SELECT AVG(not_degeri) FROM notlar WHERE ogrenci_id=?", (ogrenci_id,))
    ort = cursor.fetchone()[0]
    return ort

# Örnek kullanım
ogrenci_ekle("Ahmet")
not_ekle(1, "Matematik", 85)
not_ekle(1, "Fizik", 90)

notlari_goster()
print("Ahmet'in Ortalaması:", ortalama_hesapla(1))
