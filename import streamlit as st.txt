import streamlit as st
import time

# --- Data Bahan Makanan ---
data_makanan = {
    "🥚 Telur": {
        "reaksi": [
            (30, "Telur mulai memanas."),
            (62, "Putih telur menggumpal (albumin denaturasi)."),
            (70, "Kuning telur mengeras (lipovitellenin denaturasi)."),
        ],
        "penjelasan": "Saat telur dipanaskan, protein seperti albumin dan lipovitellenin mengalami denaturasi, mengubah struktur tiga dimensi mereka menjadi jaringan padat, mengubah tekstur dari cair menjadi padat.",
        "fun_fact": "Memasak telur di suhu rendah menghasilkan tekstur creamy sempurna!",
        "penyimpanan": {
            "suhu": "0-4°C (lemari pendingin)",
            "masa_simpan": "3-5 minggu",
            "tips": "Simpan telur di karton aslinya, jangan di pintu kulkas agar suhu lebih stabil."
        }
    },
    "🍬 Gula": {
        "reaksi": [
            (100, "Gula mulai meleleh."),
            (160, "Gula mengalami karamelisasi."),
            (180, "Gula terbakar menghasilkan rasa pahit."),
        ],
        "penjelasan": "Karamelisasi adalah reaksi pemanasan gula, memecah molekul sukrosa menjadi senyawa aromatik berwarna coklat yang meningkatkan rasa dan aroma makanan.",
        "fun_fact": "Karamelisasi dimulai sekitar 160°C dan memberikan rasa toffee yang khas!",
        "penyimpanan": {
            "suhu": "Ruang kering, suhu kamar (20-25°C)",
            "masa_simpan": "18-24 bulan",
            "tips": "Simpan di wadah kedap udara untuk mencegah penggumpalan."
        }
    },
    # Kamu bisa tambahkan makanan lain mengikuti format ini
}

# --- Fungsi Mendapatkan Semua Reaksi yang Terjadi ---
def get_multi_reaksi(makanan, suhu):
    reaksi_list = []
    for batas_suhu, reaksi in data_makanan[makanan]["reaksi"]:
        if suhu >= batas_suhu:
            reaksi_list.append(f"- {batas_suhu}°C: {reaksi}")
    if not reaksi_list:
        return ["Belum ada perubahan signifikan."]
    return reaksi_list

# --- Streamlit Layout ---
st.set_page_config(page_title="Simulasi Pemanasan Pada Pangan", page_icon="🍽️", layout="centered")

st.title("🍽️ Simulasi Pemanasan Pada Pangan")
st.write("Pilih bahan makanan 🍳, atur suhu 🌡️, dan lihat perubahan seru yang terjadi!")

# Pilih Mode
mode = st.radio("🎭 Pilih Mode Tampilan:", ("Chef Mode", "Science Mode"))

if mode == "Chef Mode":
    st.info("👨‍🍳 Jadi Chef hari ini! Fokus pada perubahan rasa, tekstur, dan tips memasak.")
else:
    st.info("🔬 Jadi Analis! Fokus pada reaksi kimia dan proses ilmiahnya.")

# Pilih bahan makanan
makanan = st.selectbox("🔍 Pilih Bahan Makanan:", list(data_makanan.keys()))

# Slider suhu atau simulasi
pilihan_simulasi = st.radio("🔥 Mau set suhu sendiri atau simulasi pemanasan?", ("Set Suhu Manual", "Simulasi Pemanasan"))

if pilihan_simulasi == "Set Suhu Manual":
    suhu = st.slider("🌡️ Atur Suhu (°C):", min_value=0, max_value=300, value=25, step=1)
else:
    suhu = 0
    progress = st.progress(0)
    for i in range(301):
        suhu = i
        progress.progress(i/300)
        time.sleep(0.01)
    st.success("Simulasi selesai!")

# Menampilkan hasil reaksi
reaksi_multi = get_multi_reaksi(makanan, suhu)

st.subheader(f"🔥 Pada suhu {suhu}°C:")
for r in reaksi_multi:
    if mode == "Chef Mode":
        if any(kata in r.lower() for kata in ["karamelisasi", "garing", "renyah", "meleleh", "harum"]):
            st.success(r)
        elif "melunak" in r.lower() or "mengering" in r.lower():
            st.warning(r)
        else:
            st.info(r)
    else:
        if "denaturasi" in r.lower() or "reaksi maillard" in r.lower():
            st.success(r)
        elif "pecah" in r.lower() or "menggumpal" in r.lower():
            st.warning(r)
        else:
            st.info(r)

# Efek suhu umum
if suhu >= 150:
    st.markdown("<h3 style='color:red;'>⚡ Reaksi besar terjadi! ⚡</h3>", unsafe_allow_html=True)
elif suhu >= 100:
    st.markdown("<h4 style='color:orange;'>🔥 Memasak aktif! Aroma khas muncul! 🔥</h4>", unsafe_allow_html=True)
elif suhu >= 50:
    st.markdown("<h5 style='color:green;'>🌡️ Proses reaksi awal terjadi. 🌡️</h5>", unsafe_allow_html=True)
else:
    st.markdown("<h5 style='color:gray;'>❄️ Hampir tidak ada reaksi. ❄️</h5>", unsafe_allow_html=True)

# Divider
st.divider()

# Penjelasan tambahan berdasarkan mode
if mode == "Chef Mode":
    st.subheader("🍳 Tips & Fun Fact untuk Chef:")
    st.info(f"💡 {data_makanan[makanan]['fun_fact']}")
else:
    st.subheader("🔬 Penjelasan Kimia Lanjut:")
    st.write(data_makanan[makanan]["penjelasan"])

# Divider
st.divider()

# Informasi Penyimpanan
st.subheader("🧊 Cara Penyimpanan Bahan Makanan:")
penyimpanan = data_makanan[makanan]["penyimpanan"]
st.write(f"• **Suhu Penyimpanan:** {penyimpanan['suhu']}")
st.write(f"• **Masa Simpan:** {penyimpanan['masa_simpan']}")
st.write(f"• **Tips:** {penyimpanan['tips']}")

# Footer
st.divider()
st.caption("🎯 Fakta: Proses pemanasan melibatkan gelatinisasi, denaturasi protein, karamelisasi, dan reaksi Maillard, membentuk karakteristik rasa dan tekstur khas pada makanan.")
