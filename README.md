
import streamlit as st
import pandas as pd
import plotly.graph_objects as go
from datetime import datetime
from pathlib import Path
from reportlab.lib.pagesizes import A4
from reportlab.lib import colors
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.units import cm
from reportlab.platypus import (
    SimpleDocTemplate, Paragraph, Spacer, Table, TableStyle, PageBreak
)
from io import BytesIO

st.set_page_config(
    page_title="NyalaBijak Simulasi IKE Rumah Tangga",
    page_icon="logo_NyalaBijak.png",
    layout="wide",
    initial_sidebar_state="collapsed"
)

# =========================================================
# STYLE
# =========================================================
st.markdown("""
<style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap');

    html, body, [class*="css"] {
        font-family: 'Inter', sans-serif;
    }

    .stApp {
        background: linear-gradient(180deg, #F4F7F1 0%, #EDF3E9 45%, #E5EDE0 100%);
        color: #14251A;
    }

    .main .block-container {
        max-width: 1240px;
        padding-top: 1.2rem;
        padding-bottom: 3rem;
    }

    .hero {
        background: linear-gradient(135deg, #12351F 0%, #1F4A2D 48%, #476B3C 100%);
        border: 1px solid rgba(255, 255, 255, 0.12);
        border-radius: 30px;
        padding: 36px;
        box-shadow: 0 18px 38px rgba(18, 53, 31, 0.22);
        margin-bottom: 18px;
    }

    .hero-title {
        font-size: 36px;
        font-weight: 900;
        line-height: 1.14;
        color: #FFFDF6;
        margin-bottom: 12px;
        letter-spacing: -0.5px;
    }

    .hero-subtitle {
        font-size: 16.5px;
        line-height: 1.7;
        color: #EAF2E5;
        max-width: 930px;
    }

    .badge {
        display: inline-block;
        background: rgba(255, 255, 255, 0.14);
        color: #F7FFF2;
        padding: 8px 13px;
        border-radius: 999px;
        font-weight: 800;
        font-size: 13px;
        margin-right: 7px;
        margin-top: 15px;
        border: 1px solid rgba(255, 255, 255, 0.18);
    }

    /* Bagian ini sengaja dibuat tanpa kotak putih besar agar tidak muncul shape kosong */
    .section {
        background: transparent;
        border-radius: 0;
        padding: 0;
        border: none;
        box-shadow: none;
        margin-bottom: 20px;
    }

    .section-title {
        font-size: 24px;
        font-weight: 900;
        color: #12351F;
        margin-bottom: 5px;
        letter-spacing: -0.25px;
    }

    .section-caption {
        color: #536453;
        font-size: 14.5px;
        line-height: 1.55;
        margin-bottom: 18px;
    }

    .info-box {
        background: #EEF7EF;
        border: 1px solid #BED8C3;
        border-radius: 16px;
        padding: 14px 16px;
        color: #24442E;
        font-size: 14px;
        line-height: 1.55;
        margin-top: 12px;
    }

    .note-box {
        background: #F8F4E8;
        border: 1px solid #DED1A8;
        border-radius: 16px;
        padding: 14px 16px;
        color: #5D4A1E;
        font-size: 14px;
        line-height: 1.55;
        margin-top: 12px;
    }

    .soft-box {
        background: #FFFFFF;
        border: 1px solid #D7E3D6;
        border-radius: 18px;
        padding: 16px 18px;
        color: #2F4033;
        font-size: 14px;
        line-height: 1.6;
        margin-top: 12px;
        box-shadow: 0 8px 20px rgba(18, 53, 31, 0.06);
    }

    .metric-card {
        background: #FFFFFF;
        border: 1px solid #D7E3D6;
        border-radius: 22px;
        padding: 20px 16px;
        text-align: center;
        min-height: 132px;
        box-shadow: 0 8px 22px rgba(18, 53, 31, 0.08);
    }

    .metric-label {
        font-size: 13px;
        color: #5B6A5D;
        font-weight: 800;
        margin-bottom: 8px;
    }

    .metric-value {
        color: #163D25;
        font-size: 27px;
        font-weight: 950;
        line-height: 1.1;
    }

    .metric-note {
        color: #758073;
        font-size: 12px;
        margin-top: 7px;
    }

    .green-box {
        background: #E7F4EA;
        border: 1px solid #B7D9BC;
        color: #1F5C2E;
        padding: 16px 18px;
        border-radius: 16px;
        line-height: 1.6;
        font-weight: 700;
    }

    .red-box {
        background: #FCECEC;
        border: 1px solid #F1B8B8;
        color: #8B2626;
        padding: 16px 18px;
        border-radius: 16px;
        line-height: 1.6;
        font-weight: 700;
    }

    .stButton > button {
        border-radius: 14px;
        background: #173D24;
        color: white;
        font-weight: 850;
        border: none;
        padding: 0.62rem 1rem;
        box-shadow: 0 8px 18px rgba(18, 53, 31, 0.16);
    }

    .stButton > button:hover {
        background: #285B35;
        color: white;
        border: none;
    }

    .stDownloadButton > button {
        border-radius: 14px;
        font-weight: 850;
        background: #173D24;
        color: white;
        border: none;
    }

    /* Samakan tampilan number_input estimasi dengan mode custom */
    div[data-testid="stNumberInput"]:has(input:disabled) {
        opacity: 1 !important;
    }

    div[data-testid="stNumberInput"]:has(input:disabled) > div,
    div[data-testid="stNumberInput"]:has(input:disabled) [data-baseweb="input"],
    div[data-testid="stNumberInput"]:has(input:disabled) [data-baseweb="base-input"] {
        opacity: 1 !important;
        background-color: #F1F3F6 !important;
    }

    div[data-testid="stNumberInput"] input:disabled,
    div[data-testid="stNumberInput"] input[disabled],
    div[data-testid="stNumberInput"] input[aria-disabled="true"] {
        color: #000000 !important;
        -webkit-text-fill-color: #000000 !important;
        caret-color: transparent !important;
        opacity: 1 !important;
        font-weight: 400 !important;
        background-color: #F1F3F6 !important;
        cursor: default !important;
    }

    /* Label input estimasi tetap hitam dan tidak pudar */
    div[data-testid="stNumberInput"]:has(input:disabled) label,
    div[data-testid="stNumberInput"]:has(input:disabled) label p,
    div[data-testid="stNumberInput"]:has(input:disabled) [data-testid="stWidgetLabel"],
    div[data-testid="stNumberInput"]:has(input:disabled) [data-testid="stWidgetLabel"] p {
        color: #000000 !important;
        -webkit-text-fill-color: #000000 !important;
        opacity: 1 !important;
        font-weight: 400 !important;
    }

    /* Hilangkan tombol minus dan plus hanya pada input watt estimasi */
    div[data-testid="stNumberInput"]:has(input:disabled) button {
        display: none !important;
    }

</style>
""", unsafe_allow_html=True)

# =========================================================
# LOAD DATA
# =========================================================
@st.cache_data
def load_data():
    base = Path(__file__).parent
    alat = pd.read_csv(base / "data_alat.csv")
    tarif = pd.read_csv(base / "tarif_pln.csv")
    return alat, tarif

df_alat, df_tarif = load_data()

# =========================================================
# SESSION STATE
# =========================================================
if "alat_terpilih" not in st.session_state:
    st.session_state.alat_terpilih = []

# Kondisi awal hanya disimpan ketika hasil simulasi sudah masuk kategori tidak hemat.
if "kondisi_awal" not in st.session_state:
    st.session_state.kondisi_awal = None

if "hasil_perbandingan" not in st.session_state:
    st.session_state.hasil_perbandingan = None

# =========================================================
# HELPER
# =========================================================
def rupiah(value):
    return f"Rp {value:,.0f}".replace(",", ".")

def safe_float(x, default=0.0):
    try:
        if pd.isna(x):
            return default
        return float(x)
    except Exception:
        return default

def pilih_watt(row, mode, watt_custom):
    if mode == "Estimasi daya rendah / watt minimum":
        return safe_float(row["Watt Min"], safe_float(row["Watt Umum"], 0))
    if mode == "Estimasi daya tinggi / watt maksimum":
        return safe_float(row["Watt Maks"], safe_float(row["Watt Umum"], 0))
    if mode == "Custom sesuai label alat":
        return safe_float(watt_custom, safe_float(row["Watt Umum"], 0))
    return safe_float(row["Watt Umum"], 0)

def hitung_kwh(watt, faktor_aktif, jumlah, jam, hari):
    return (float(watt) * float(faktor_aktif) * float(jumlah) * float(jam) * float(hari)) / 1000

def kategori_ike(ike):
    if ike < 10:
        return "HEMAT", "Nilai IKE berada di bawah 10 kWh/m²/bulan."
    return "TIDAK HEMAT", "Nilai IKE mencapai atau melebihi 10 kWh/m²/bulan."

def kategori_visual(ike):
    if ike <= 5:
        return "Sangat hemat"
    if ike < 10:
        return "Hemat"
    if ike <= 15:
        return "Tidak hemat"
    return "Sangat tidak hemat"

def tambah_alat(item):
    st.session_state.alat_terpilih.append(item)
    st.session_state.hasil_perbandingan = None

def get_selected_df():
    if not st.session_state.alat_terpilih:
        return pd.DataFrame()
    return pd.DataFrame(st.session_state.alat_terpilih)


def buat_pdf_ringkasan(df, luas_rumah, tarif, total_kwh, total_biaya, ike, kategori, saran):
    buffer = BytesIO()
    doc = SimpleDocTemplate(
        buffer,
        pagesize=A4,
        rightMargin=1.6 * cm,
        leftMargin=1.6 * cm,
        topMargin=1.5 * cm,
        bottomMargin=1.5 * cm,
    )

    styles = getSampleStyleSheet()
    title_style = ParagraphStyle(
        "CustomTitle",
        parent=styles["Title"],
        fontSize=16,
        leading=20,
        textColor=colors.HexColor("#173D24"),
        spaceAfter=12,
    )
    heading_style = ParagraphStyle(
        "CustomHeading",
        parent=styles["Heading2"],
        fontSize=12,
        leading=15,
        textColor=colors.HexColor("#173D24"),
        spaceBefore=10,
        spaceAfter=6,
    )
    normal_style = ParagraphStyle(
        "CustomNormal",
        parent=styles["Normal"],
        fontSize=9,
        leading=12,
        textColor=colors.HexColor("#243326"),
    )

    story = []
    story.append(Paragraph("Ringkasan Hasil Simulasi Penggunaan Listrik Rumah Tangga", title_style))
    story.append(Paragraph(f"Tanggal simulasi: {datetime.now().strftime('%d-%m-%Y %H:%M')}", normal_style))
    story.append(Spacer(1, 8))

    ringkasan_data = [
        ["Luas rumah/ruangan", f"{luas_rumah:.2f} m²"],
        ["Tarif listrik", f"{rupiah(tarif)} per kWh"],
        ["Total konsumsi", f"{total_kwh:.2f} kWh/bulan"],
        ["Estimasi biaya", f"{rupiah(total_biaya)} per bulan"],
        ["Nilai IKE", f"{ike:.2f} kWh/m²/bulan"],
        ["Kategori", kategori],
    ]

    table = Table(ringkasan_data, colWidths=[6 * cm, 10 * cm])
    table.setStyle(TableStyle([
        ("BACKGROUND", (0, 0), (0, -1), colors.HexColor("#EAF7ED")),
        ("TEXTCOLOR", (0, 0), (-1, -1), colors.HexColor("#173D24")),
        ("FONTNAME", (0, 0), (-1, -1), "Helvetica"),
        ("FONTSIZE", (0, 0), (-1, -1), 9),
        ("GRID", (0, 0), (-1, -1), 0.4, colors.HexColor("#C9D9CB")),
        ("VALIGN", (0, 0), (-1, -1), "TOP"),
        ("ROWBACKGROUNDS", (0, 0), (-1, -1), [colors.white, colors.HexColor("#F7FAF6")]),
        ("LEFTPADDING", (0, 0), (-1, -1), 7),
        ("RIGHTPADDING", (0, 0), (-1, -1), 7),
        ("TOPPADDING", (0, 0), (-1, -1), 6),
        ("BOTTOMPADDING", (0, 0), (-1, -1), 6),
    ]))
    story.append(table)

    story.append(Paragraph("Daftar Alat yang Dimasukkan", heading_style))
    if df.empty:
        story.append(Paragraph("Belum ada alat yang ditambahkan.", normal_style))
    else:
        alat_data = [["No", "Alat", "Kapasitas", "Watt", "Jumlah", "Jam/Hari", "kWh/Bulan"]]
        for i, (_, row) in enumerate(df.iterrows(), start=1):
            alat_data.append([
                str(i),
                Paragraph(str(row["Nama Alat"]), normal_style),
                Paragraph(str(row["Kapasitas/Satuan"]), normal_style),
                f"{row['Watt Dipakai']}",
                f"{row['Jumlah']}",
                f"{row['Jam/Hari']}",
                f"{row['Konsumsi (kWh/Bulan)']}",
            ])

        alat_table = Table(alat_data, colWidths=[0.8*cm, 4.0*cm, 3.0*cm, 1.8*cm, 1.6*cm, 2.0*cm, 2.4*cm])
        alat_table.setStyle(TableStyle([
            ("BACKGROUND", (0, 0), (-1, 0), colors.HexColor("#26903C")),
            ("TEXTCOLOR", (0, 0), (-1, 0), colors.white),
            ("FONTNAME", (0, 0), (-1, 0), "Helvetica-Bold"),
            ("FONTSIZE", (0, 0), (-1, -1), 7.5),
            ("GRID", (0, 0), (-1, -1), 0.35, colors.HexColor("#C9D9CB")),
            ("VALIGN", (0, 0), (-1, -1), "TOP"),
            ("ROWBACKGROUNDS", (1, 1), (-1, -1), [colors.white, colors.HexColor("#F7FAF6")]),
            ("LEFTPADDING", (0, 0), (-1, -1), 4),
            ("RIGHTPADDING", (0, 0), (-1, -1), 4),
            ("TOPPADDING", (0, 0), (-1, -1), 4),
            ("BOTTOMPADDING", (0, 0), (-1, -1), 4),
        ]))
        story.append(alat_table)

    story.append(Paragraph("Saran Penghematan", heading_style))
    for item in saran:
        story.append(Paragraph(f"• {item}", normal_style))

    story.append(Spacer(1, 8))
    story.append(Paragraph(
        "Catatan: Daya alat pada database merupakan estimasi untuk simulasi edukatif. "
        "Jika pengguna mengetahui daya aktual dari label alat, nilai watt dapat disesuaikan melalui mode custom.",
        normal_style
    ))

    doc.build(story)
    buffer.seek(0)
    return buffer.getvalue()

def build_suggestions(df, ike):
    if df.empty:
        return ["Tambahkan beberapa alat listrik rumah tangga terlebih dahulu agar saran penghematan dapat ditampilkan."]

    suggestions = []
    top = df.sort_values("Konsumsi (kWh/Bulan)", ascending=False).iloc[0]
    suggestions.append(
        f"Konsumsi terbesar berasal dari {top['Nama Alat']} ({top['Kapasitas/Satuan']}) sebesar "
        f"{top['Konsumsi (kWh/Bulan)']:.2f} kWh/bulan. Prioritaskan evaluasi pada alat ini."
    )

    names = " ".join(df["Nama Alat"].astype(str).str.lower().tolist())
    kelompok = " ".join(df["Kelompok"].astype(str).str.lower().tolist())

    if "ac" in names or "pendingin" in kelompok:
        suggestions.append("Untuk AC/pendingin, coba kurangi durasi pemakaian, gunakan suhu sekitar 24–26°C, dan pastikan ruangan tertutup saat AC menyala.")
    if "lampu" in names:
        suggestions.append("Untuk penerangan, matikan lampu pada ruangan kosong dan manfaatkan cahaya alami pada siang hari.")
    if "kulkas" in names or "freezer" in names:
        suggestions.append("Untuk kulkas/freezer, jangan terlalu sering membuka pintu terlalu lama dan atur suhu sesuai kebutuhan.")
    if "rice cooker" in names or "magic com" in names:
        suggestions.append("Untuk rice cooker/magic com, hindari mode warm terlalu lama karena tetap menambah konsumsi energi.")
    if "water heater" in names or "pemanas" in kelompok:
        suggestions.append("Untuk pemanas air, gunakan seperlunya karena alat ini umumnya memiliki daya besar.")
    if "setrika" in names:
        suggestions.append("Gunakan setrika secara sekaligus untuk beberapa pakaian agar waktu pemakaian lebih efisien.")
    if "charger" in names or "standby" in names:
        suggestions.append("Cabut charger dan perangkat standby yang tidak digunakan karena tetap dapat memakai listrik walaupun kecil.")

    if ike >= 10:
        suggestions.append("Karena IKE masuk kategori tidak hemat, coba kurangi jam penggunaan alat terbesar lalu bandingkan hasilnya kembali.")
    else:
        suggestions.append("Nilai IKE masih hemat. Pertahankan kebiasaan mematikan perangkat listrik yang tidak digunakan.")

    return suggestions

# =========================================================
# HERO
# =========================================================
st.markdown("""
<div class="hero">
    <div class="hero-title"> Simulasi Penggunaan Listrik Rumah Tangga</div>
    <div class="hero-subtitle">
        Pilih beberapa alat listrik yang digunakan di rumah, masukkan jumlah dan lama pemakaian,
        lalu sistem akan menjumlahkan seluruh konsumsi alat untuk menghitung total kWh, estimasi biaya,
        kategori hemat/tidak hemat, dan saran penghematan. Perhitungan kategori menggunakan dasar IKE
        yang dijelaskan pada bagian petunjuk simulasi.
    </div>
    <span class="badge">Bisa tambah banyak alat</span>
    <span class="badge">Berdasarkan database alat</span>
    <span class="badge">Pengguna tidak wajib tahu watt</span>
    <span class="badge">Kategori dihitung dari total alat</span>
</div>
""", unsafe_allow_html=True)

st.info(
    "Petunjuk: Tambahkan alat listrik yang digunakan di rumah ke dalam daftar. "
    "Setelah selesai melihat hasil simulasi, tutup tab simulasi ini atau klik tab media interaktif "
    "yang masih terbuka di browser."
)

with st.expander("Cara kerja dan formulasi simulasi", expanded=False):
    st.write(
        "Simulasi ini menggunakan sistem daftar alat. Pengguna dapat menambahkan beberapa alat listrik "
        "yang digunakan di rumah, kemudian sistem menjumlahkan konsumsi seluruh alat tersebut. "
        "Hasil akhir berupa total konsumsi energi, estimasi biaya, nilai IKE, kategori hemat atau tidak hemat, "
        "serta saran penghematan."
    )

    st.markdown("#### 1. Konsumsi energi listrik tiap alat")
    st.latex(r"E_i = \frac{P_i \times F_i \times N_i \times t_i \times d_i}{1000}")
    st.markdown(
        """
        <div class="note-box">
            <b>Keterangan:</b><br>
            <b>E<sub>i</sub></b> = energi listrik alat ke-i per bulan (kWh/bulan)<br>
            <b>P<sub>i</sub></b> = daya alat listrik ke-i (Watt)<br>
            <b>F<sub>i</sub></b> = faktor aktif alat<br>
            <b>N<sub>i</sub></b> = jumlah unit alat<br>
            <b>t<sub>i</sub></b> = lama penggunaan alat per hari (jam/hari)<br>
            <b>d<sub>i</sub></b> = jumlah hari penggunaan dalam satu bulan (hari/bulan)<br>
            <b>1000</b> = faktor konversi dari Wh ke kWh
        </div>
        """,
        unsafe_allow_html=True
    )

    st.markdown("#### 2. Total konsumsi energi listrik bulanan")
    st.latex(r"E_m = \sum_{i=1}^{n} E_i")
    st.markdown(
        """
        <div class="note-box">
            <b>Keterangan:</b><br>
            <b>E<sub>m</sub></b> = total energi listrik per bulan (kWh/bulan)<br>
            <b>E<sub>i</sub></b> = energi listrik alat ke-i per bulan (kWh/bulan)<br>
            <b>n</b> = jumlah alat listrik yang dimasukkan pengguna
        </div>
        """,
        unsafe_allow_html=True
    )

    st.markdown("#### 3. Intensitas Konsumsi Energi")
    st.latex(r"IKE = \frac{E_m}{A}")
    st.markdown(
        """
        <div class="note-box">
            <b>Keterangan:</b><br>
            <b>IKE</b> = Intensitas Konsumsi Energi (kWh/m²/bulan)<br>
            <b>E<sub>m</sub></b> = total energi listrik per bulan (kWh/bulan)<br>
            <b>A</b> = luas rumah atau ruangan (m²)
        </div>
        """,
        unsafe_allow_html=True
    )

    st.markdown("#### 4. Estimasi biaya listrik")
    st.latex(r"B_m = E_m \times T")
    st.markdown(
        """
        <div class="note-box">
            <b>Keterangan:</b><br>
            <b>B<sub>m</sub></b> = estimasi biaya listrik per bulan (Rp/bulan)<br>
            <b>E<sub>m</sub></b> = total energi listrik per bulan (kWh/bulan)<br>
            <b>T</b> = tarif listrik per kWh (Rp/kWh)
        </div>
        """,
        unsafe_allow_html=True
    )

    st.markdown("#### 5. Dasar acuan perhitungan")
    st.markdown(
        """
        <div class="soft-box">
            Dasar perhitungan dalam simulasi ini menggunakan konsep daya dan konsumsi energi listrik.
            Total daya nominal diperoleh dari penjumlahan daya setiap peralatan berdasarkan jumlah unitnya.
            Konsumsi energi listrik dihitung berdasarkan daya, jumlah peralatan, faktor aktif, lama penggunaan
            harian, dan jumlah hari pemakaian, kemudian dikonversi dari Wh menjadi kWh. Perhitungan
            Intensitas Konsumsi Energi (IKE) dilakukan dengan membandingkan konsumsi energi listrik bulanan
            terhadap luas rumah, sedangkan estimasi biaya dihitung berdasarkan konsumsi energi dan tarif
            listrik yang digunakan sesuai golongan pelanggan.
            <br><br>
            Konsep IKE sejalan dengan Permen ESDM Nomor 3 Tahun 2025 tentang Konservasi Energi oleh Pemerintah
            dan Pemerintah Daerah. Prinsip efisiensi energi bangunan didukung oleh Permen PUPR Nomor 21
            Tahun 2021 tentang Penilaian Kinerja Bangunan Gedung Hijau, sedangkan prinsip pengelolaan energi
            sejalan dengan Permen ESDM Nomor 8 Tahun 2025 tentang Manajemen Energi. Adapun penggolongan tarif
            listrik mengacu pada Permen ESDM Nomor 7 Tahun 2024 tentang Tarif Tenaga Listrik yang Disediakan
            oleh PT PLN (Persero).
            <br><br>
            Dalam penelitian ini, IKE digunakan sebagai pendekatan simulasi edukatif pada lingkup rumah
            tangga. Kategori hemat atau tidak hemat ditentukan berdasarkan kriteria penelitian sesuai
            masukan validator materi, bukan berdasarkan klasifikasi resmi dalam Permen ESDM Nomor 3 Tahun
            2025. Hasil simulasi bertujuan membantu pengguna memahami konsumsi energi dan estimasi biaya
            listrik, bukan sebagai hasil audit energi profesional maupun tagihan resmi PLN.
        </div>
        """,
        unsafe_allow_html=True
    )

# =========================================================
# DATA RUMAH
# =========================================================
st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">1. Data Rumah</div>', unsafe_allow_html=True)
st.markdown('<div class="section-caption">Masukkan luas rumah/ruangan. Luas ini menjadi dasar pembagi untuk menghitung IKE.</div>', unsafe_allow_html=True)

tarif_options = []
tarif_map = {}
for _, r in df_tarif.iterrows():
    label = f"{r['Golongan']} - {r['Daya']} ({rupiah(r['Tarif Rp/kWh'])}/kWh)"
    tarif_options.append(label)
    tarif_map[label] = float(r["Tarif Rp/kWh"])

default_label = None
for opt in tarif_options:
    if "1.300" in opt or "2.200" in opt:
        default_label = opt
        break
if default_label is None and tarif_options:
    default_label = tarif_options[0]

data_rumah_dikunci = st.session_state.kondisi_awal is not None

col_home1, col_home2, col_home3 = st.columns([1, 1.35, 1])
with col_home1:
    luas_rumah = st.number_input(
        "Luas rumah/ruangan (m²)",
        min_value=1.0,
        max_value=1000.0,
        value=36.0,
        step=1.0,
        disabled=data_rumah_dikunci,
        help="Luas rumah dikunci setelah kondisi awal disimpan agar perbandingan IKE menggunakan dasar yang sama."
    )
with col_home2:
    tarif_label = st.selectbox(
        "Pilih golongan tarif listrik",
        tarif_options,
        index=tarif_options.index(default_label),
        disabled=data_rumah_dikunci,
        help="Golongan tarif dikunci setelah kondisi awal disimpan agar perbandingan biaya tetap setara."
    )
with col_home3:
    tarif_manual = st.number_input(
        "Tarif yang dipakai (Rp/kWh)",
        min_value=0.0,
        max_value=10000.0,
        value=tarif_map[tarif_label],
        step=50.0,
        disabled=data_rumah_dikunci,
        help="Tarif dikunci setelah kondisi awal disimpan agar perbandingan biaya menggunakan tarif yang sama."
    )

if data_rumah_dikunci:
    st.caption(
        "Luas rumah dan tarif dikunci selama skenario perbandingan berlangsung. "
        "Hapus kondisi awal jika ingin mengubah data rumah."
    )

st.markdown("""
<div class="info-box">
    <b>Batas kategori:</b> IKE &lt; 10 kWh/m²/bulan = hemat, sedangkan IKE ≥ 10 kWh/m²/bulan = tidak hemat.
</div>
""", unsafe_allow_html=True)
st.markdown('</div>', unsafe_allow_html=True)

# =========================================================
# TAMBAH ALAT
# =========================================================
st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">2. Tambahkan Alat ke Daftar Rumah</div>', unsafe_allow_html=True)
st.markdown('<div class="section-caption">Ulangi langkah ini sampai alat yang digunakan di rumah sudah masuk ke daftar. Pengguna dapat mencari alat langsung, memilih berdasarkan kategori, atau menambahkan alat manual jika belum tersedia.</div>', unsafe_allow_html=True)

tab1, tab2, tab3 = st.tabs(["Pilih dari database alat", "Input alat manual", "Lihat database alat"])

with tab1:
    mode_pilih = st.radio(
        "Metode memilih alat",
        ["Cari alat langsung", "Pilih berdasarkan kategori"],
        horizontal=True,
        help="Gunakan fitur cari jika ingin menemukan alat lebih cepat. Gunakan kategori jika ingin menjelajahi alat berdasarkan kelompok."
    )

    if mode_pilih == "Cari alat langsung":
        kata_kunci = st.text_input(
            "Cari nama alat atau kapasitas",
            placeholder="Contoh: AC 1 PK, lampu LED 10 watt, kulkas 2 pintu, TV 43 inch, setrika"
        )

        df_cari = df_alat.copy()
        df_cari["Label Pencarian"] = (
            df_cari["Kelompok"].astype(str) + " — " +
            df_cari["Nama Alat"].astype(str) + " — " +
            df_cari["Kapasitas/Satuan"].astype(str)
        )

        if kata_kunci.strip():
            keywords = kata_kunci.lower().split()
            mask = df_cari["Label Pencarian"].str.lower().apply(
                lambda text: all(k in text for k in keywords)
            )
            df_cari = df_cari[mask].copy()

        if df_cari.empty:
            st.warning("Alat tidak ditemukan. Coba kata kunci lain atau gunakan tab Input alat manual.")
            st.stop()

        alat_labels = df_cari["Label Pencarian"].tolist()
        alat_label = st.selectbox(
            "Pilih hasil pencarian",
            alat_labels,
            help="Jika daftar masih terlalu banyak, ketik kata kunci yang lebih spesifik."
        )
        row_index = alat_labels.index(alat_label)
        row = df_cari.iloc[row_index]

    else:
        c1, c2 = st.columns([1, 1])
        with c1:
            kelompok_list = sorted(df_alat["Kelompok"].dropna().unique().tolist())
            kelompok = st.selectbox("Kategori/kelompok alat", kelompok_list)
        with c2:
            df_kel = df_alat[df_alat["Kelompok"] == kelompok].copy()
            alat_labels = (
                df_kel["Nama Alat"].astype(str) + " — " + df_kel["Kapasitas/Satuan"].astype(str)
            ).tolist()
            alat_label = st.selectbox("Pilih alat dan kapasitas/varian", alat_labels)

        row_index = alat_labels.index(alat_label)
        row = df_kel.iloc[row_index]

    c3, c4, c5, c6 = st.columns(4)
    with c3:
        mode_watt = st.selectbox(
            "Mode watt",
            [
                "Estimasi daya umum / rekomendasi",
                "Estimasi daya rendah / watt minimum",
                "Estimasi daya tinggi / watt maksimum",
                "Custom sesuai label alat",
            ]
        )
    with c4:
        watt_default = pilih_watt(row, mode_watt, row["Watt Umum"])

        if mode_watt == "Custom sesuai label alat":
            watt_custom = st.number_input(
                "Daya watt yang dipakai",
                min_value=1.0,
                max_value=10000.0,
                value=float(watt_default),
                step=1.0,
                help="Masukkan daya aktual sesuai label atau nameplate alat."
            )
        else:
            watt_custom = st.number_input(
                "Daya watt yang dipakai",
                min_value=1.0,
                max_value=10000.0,
                value=float(watt_default),
                step=1.0,
                disabled=True,
                key=f"watt_estimasi_{row.name}_{mode_watt}"
            )
    with c5:
        jumlah = st.number_input("Jumlah unit", min_value=1, max_value=100, value=1, step=1)
    with c6:
        hari = st.number_input("Hari penggunaan/bulan", min_value=1, max_value=31, value=30, step=1)

    faktor_aktif = safe_float(row["Faktor Aktif Saran"], 1)
    jam_default = min(max(safe_float(row["Jam Default/Hari"], 2), 0), 24)
    jam = st.slider("Jam penggunaan per hari", min_value=0.0, max_value=24.0, value=float(jam_default), step=0.5)

    watt_pakai = pilih_watt(row, mode_watt, watt_custom)
    kwh_preview = hitung_kwh(watt_pakai, faktor_aktif, jumlah, jam, hari)

    st.markdown(
        f"""
        <div class="soft-box">
            <b>Informasi alat:</b><br>
            Nama alat: <b>{row['Nama Alat']}</b><br>
            Kapasitas/satuan: <b>{row['Kapasitas/Satuan']}</b><br>
            Estimasi daya rendah–umum–tinggi: <b>{row['Watt Min']} W – {row['Watt Umum']} W – {row['Watt Maks']} W</b><br>
            Faktor aktif bawaan: <b>{faktor_aktif}</b><br>
            Keterangan: {row['Keterangan Untuk Simulasi']}<br>
            Pratinjau konsumsi jika ditambahkan: <b>{kwh_preview:.2f} kWh/bulan</b>
        </div>
        """,
        unsafe_allow_html=True
    )

    if st.button("+ Tambahkan alat ini ke daftar rumah", use_container_width=True):
        tambah_alat({
            "Sumber": "Database",
            "Kelompok": row["Kelompok"],
            "Nama Alat": row["Nama Alat"],
            "Kapasitas/Satuan": row["Kapasitas/Satuan"],
            "Mode Watt": mode_watt,
            "Watt Dipakai": round(watt_pakai, 2),
            "Faktor Aktif": round(faktor_aktif, 3),
            "Jumlah": int(jumlah),
            "Jam/Hari": float(jam),
            "Hari/Bulan": int(hari),
            "Konsumsi (kWh/Bulan)": round(kwh_preview, 2),
            "Keterangan": row["Keterangan Untuk Simulasi"],
        })
        st.success(f"{row['Nama Alat']} berhasil ditambahkan. Silakan tambahkan alat lain jika masih ada.")

with tab2:
    st.write("Gunakan bagian ini jika alat pengguna tidak tersedia pada database.")
    m1, m2, m3 = st.columns([1, 1, 1])
    with m1:
        manual_nama = st.text_input("Nama alat manual", placeholder="Contoh: Mesin aquarium besar")
    with m2:
        manual_kapasitas = st.text_input("Kapasitas/keterangan", placeholder="Contoh: 1 unit / ukuran sedang")
    with m3:
        manual_kelompok = st.selectbox("Kategori alat manual", ["Alat tambahan/manual"] + sorted(df_alat["Kelompok"].dropna().unique().tolist()))

    m4, m5, m6, m7 = st.columns(4)
    with m4:
        manual_watt = st.number_input("Daya sesuai label alat (watt)", min_value=1.0, max_value=10000.0, value=100.0, step=1.0)
    with m5:
        manual_jumlah = st.number_input("Jumlah unit manual", min_value=1, max_value=100, value=1, step=1)
    with m6:
        manual_jam = st.slider("Jam penggunaan manual per hari", min_value=0.0, max_value=24.0, value=1.0, step=0.5)
    with m7:
        manual_hari = st.number_input("Hari penggunaan manual/bulan", min_value=1, max_value=31, value=30, step=1)

    manual_faktor = st.slider(
        "Faktor aktif manual",
        min_value=0.1,
        max_value=1.0,
        value=1.0,
        step=0.05,
        help="Gunakan 1.0 jika alat bekerja terus saat menyala. Untuk alat kompresor/otomatis, nilainya bisa lebih kecil."
    )

    manual_kwh = hitung_kwh(manual_watt, manual_faktor, manual_jumlah, manual_jam, manual_hari)
    st.caption(f"Pratinjau konsumsi alat manual: {manual_kwh:.2f} kWh/bulan")

    if st.button("+ Tambahkan alat manual ke daftar rumah", use_container_width=True):
        if not manual_nama.strip():
            st.error("Nama alat manual belum diisi.")
        else:
            tambah_alat({
                "Sumber": "Manual",
                "Kelompok": manual_kelompok,
                "Nama Alat": manual_nama.strip(),
                "Kapasitas/Satuan": manual_kapasitas.strip() if manual_kapasitas.strip() else "-",
                "Mode Watt": "Custom manual",
                "Watt Dipakai": round(manual_watt, 2),
                "Faktor Aktif": round(manual_faktor, 3),
                "Jumlah": int(manual_jumlah),
                "Jam/Hari": float(manual_jam),
                "Hari/Bulan": int(manual_hari),
                "Konsumsi (kWh/Bulan)": round(manual_kwh, 2),
                "Keterangan": "Alat ditambahkan manual oleh pengguna.",
            })
            st.success(f"{manual_nama} berhasil ditambahkan ke daftar.")

with tab3:
    st.write("Database alat yang digunakan pada simulasi.")
    st.dataframe(
        df_alat[[
            "Kelompok", "Nama Alat", "Kapasitas/Satuan", "Watt Min", "Watt Umum", "Watt Maks",
            "Faktor Aktif Saran", "Jam Default/Hari", "Keterangan Untuk Simulasi"
        ]],
        use_container_width=True,
        hide_index=True
    )
    st.caption(f"Jumlah data alat pada database: {len(df_alat)} baris. Jika alat belum tersedia, gunakan tab Input alat manual.")

st.markdown('</div>', unsafe_allow_html=True)

# =========================================================
# DAFTAR ALAT TERPILIH
# =========================================================
df_selected = get_selected_df()

st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">3. Daftar Alat yang Sudah Dimasukkan</div>', unsafe_allow_html=True)
st.markdown('<div class="section-caption">Kategori hemat/tidak hemat akan dihitung dari total seluruh alat pada daftar ini.</div>', unsafe_allow_html=True)

if df_selected.empty:
    st.warning("Belum ada alat yang ditambahkan. Tambahkan beberapa alat rumah tangga terlebih dahulu.")
else:
    st.caption(
        "Nilai watt, jumlah unit, jam penggunaan, dan hari penggunaan dapat disesuaikan langsung pada tabel. "
        "Tekan “Terapkan Perubahan Daftar” agar konsumsi setiap alat dihitung ulang."
    )

    kolom_editor = [
        "Kelompok", "Nama Alat", "Kapasitas/Satuan", "Watt Dipakai", "Faktor Aktif",
        "Jumlah", "Jam/Hari", "Hari/Bulan", "Konsumsi (kWh/Bulan)"
    ]

    df_editor = st.data_editor(
        df_selected[kolom_editor],
        use_container_width=True,
        hide_index=True,
        disabled=[
            "Kelompok", "Nama Alat", "Kapasitas/Satuan",
            "Faktor Aktif", "Konsumsi (kWh/Bulan)"
        ],
        column_config={
            "Watt Dipakai": st.column_config.NumberColumn(
                "Watt Dipakai",
                min_value=1.0,
                max_value=10000.0,
                step=1.0,
                format="%.2f"
            ),
            "Jumlah": st.column_config.NumberColumn(
                "Jumlah",
                min_value=1,
                max_value=100,
                step=1
            ),
            "Jam/Hari": st.column_config.NumberColumn(
                "Jam/Hari",
                min_value=0.0,
                max_value=24.0,
                step=0.5,
                format="%.1f"
            ),
            "Hari/Bulan": st.column_config.NumberColumn(
                "Hari/Bulan",
                min_value=1,
                max_value=31,
                step=1
            ),
            "Konsumsi (kWh/Bulan)": st.column_config.NumberColumn(
                "Konsumsi (kWh/Bulan)",
                format="%.2f"
            ),
        },
        key="editor_daftar_alat"
    )

    if st.button("Terapkan Perubahan Daftar", use_container_width=True, type="primary"):
        daftar_baru = []
        for i, edited_row in df_editor.iterrows():
            item_asli = dict(st.session_state.alat_terpilih[i])

            watt_baru = max(1.0, safe_float(edited_row["Watt Dipakai"], 1.0))
            jumlah_baru = max(1, int(safe_float(edited_row["Jumlah"], 1)))
            jam_baru = min(24.0, max(0.0, safe_float(edited_row["Jam/Hari"], 0.0)))
            hari_baru = min(31, max(1, int(safe_float(edited_row["Hari/Bulan"], 1))))
            faktor_baru = safe_float(item_asli.get("Faktor Aktif", 1.0), 1.0)

            item_asli["Watt Dipakai"] = round(watt_baru, 2)
            item_asli["Jumlah"] = jumlah_baru
            item_asli["Jam/Hari"] = jam_baru
            item_asli["Hari/Bulan"] = hari_baru
            item_asli["Konsumsi (kWh/Bulan)"] = round(
                hitung_kwh(
                    watt_baru,
                    faktor_baru,
                    jumlah_baru,
                    jam_baru,
                    hari_baru
                ),
                2
            )
            daftar_baru.append(item_asli)

        st.session_state.alat_terpilih = daftar_baru
        st.session_state.hasil_perbandingan = None
        st.rerun()

    d1, d2 = st.columns([2, 1])
    with d1:
        idx = st.selectbox(
            "Pilih alat yang ingin dihapus",
            options=list(range(len(df_selected))),
            format_func=lambda i: f"{i+1}. {df_selected.iloc[i]['Nama Alat']} — {df_selected.iloc[i]['Kapasitas/Satuan']}"
        )
    with d2:
        st.write("")
        st.write("")
        if st.button("Hapus alat", use_container_width=True):
            st.session_state.alat_terpilih.pop(idx)
            st.session_state.hasil_perbandingan = None
            st.rerun()

    if st.button("Reset semua alat", use_container_width=True):
        st.session_state.alat_terpilih = []
        st.session_state.kondisi_awal = None
        st.session_state.hasil_perbandingan = None
        st.session_state.pop("editor_daftar_alat", None)
        st.rerun()

st.markdown('</div>', unsafe_allow_html=True)

# =========================================================
# HITUNGAN UTAMA
# =========================================================
if df_selected.empty:
    total_kwh = 0.0
else:
    total_kwh = float(df_selected["Konsumsi (kWh/Bulan)"].sum())

total_biaya = total_kwh * tarif_manual
ike = total_kwh / luas_rumah if luas_rumah > 0 else 0
cat, cat_note = kategori_ike(ike)
vis_cat = kategori_visual(ike)

# =========================================================
# HASIL
# =========================================================
st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">4. Hasil Simulasi Berdasarkan Seluruh Daftar Alat</div>', unsafe_allow_html=True)
st.markdown('<div class="section-caption">Hasil ini bukan dari satu alat, tetapi dari akumulasi seluruh alat yang sudah ditambahkan pengguna.</div>', unsafe_allow_html=True)

m1, m2, m3, m4 = st.columns(4)
with m1:
    st.markdown(f"""
    <div class="metric-card">
        <div class="metric-label">Total Konsumsi</div>
        <div class="metric-value">{total_kwh:.2f}</div>
        <div class="metric-note">kWh/bulan</div>
    </div>
    """, unsafe_allow_html=True)
with m2:
    st.markdown(f"""
    <div class="metric-card">
        <div class="metric-label">Estimasi Biaya</div>
        <div class="metric-value">{rupiah(total_biaya)}</div>
        <div class="metric-note">per bulan</div>
    </div>
    """, unsafe_allow_html=True)
with m3:
    st.markdown(f"""
    <div class="metric-card">
        <div class="metric-label">Nilai IKE</div>
        <div class="metric-value">{ike:.2f}</div>
        <div class="metric-note">kWh/m²/bulan</div>
    </div>
    """, unsafe_allow_html=True)
with m4:
    st.markdown(f"""
    <div class="metric-card">
        <div class="metric-label">Kategori</div>
        <div class="metric-value">{cat}</div>
        <div class="metric-note">{vis_cat}</div>
    </div>
    """, unsafe_allow_html=True)

st.markdown('</div>', unsafe_allow_html=True)

# =========================================================
# SKENARIO PERBANDINGAN PENGHEMATAN
# =========================================================
st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">5. Skenario Perbandingan Penghematan</div>', unsafe_allow_html=True)

if df_selected.empty:
    st.markdown(
        '<div class="section-caption">Fitur perbandingan akan tersedia setelah alat ditambahkan dan hasil simulasi mencapai kategori tidak hemat.</div>',
        unsafe_allow_html=True
    )

elif st.session_state.kondisi_awal is None:
    if ike < 10:
        st.markdown(
            '<div class="section-caption">Simulasi masih berada pada kategori hemat. '
            'Anda tetap dapat menambahkan, menghapus, atau menyesuaikan alat seperti biasa. '
            'Fitur perbandingan baru akan muncul apabila nilai IKE mencapai kategori tidak hemat.</div>',
            unsafe_allow_html=True
        )
        st.markdown(
            f'<div class="green-box">Nilai IKE saat ini {ike:.2f} kWh/m²/bulan dan masih termasuk kategori hemat.</div>',
            unsafe_allow_html=True
        )
    else:
        st.markdown(
            '<div class="section-caption">Hasil simulasi sudah masuk kategori tidak hemat. '
            'Simpan keadaan ini sebagai kondisi awal, lalu lakukan perubahan pada daftar alat '
            'untuk melihat dampak penghematan.</div>',
            unsafe_allow_html=True
        )
        st.markdown(
            f'<div class="red-box">Kondisi saat ini: {total_kwh:.2f} kWh/bulan, '
            f'IKE {ike:.2f} kWh/m²/bulan, kategori {cat}.</div>',
            unsafe_allow_html=True
        )

        if st.button(
            "Simpan Kondisi Tidak Hemat sebagai Kondisi Awal",
            use_container_width=True,
            type="primary"
        ):
            st.session_state.kondisi_awal = {
                "daftar_alat": df_selected.copy(),
                "luas_rumah": float(luas_rumah),
                "tarif": float(tarif_manual),
                "total_kwh": float(total_kwh),
                "total_biaya": float(total_biaya),
                "ike": float(ike),
                "kategori": cat,
            }
            st.session_state.hasil_perbandingan = None
            st.rerun()

else:
    awal = st.session_state.kondisi_awal

    st.markdown(
        '<div class="section-caption">Kondisi awal sudah disimpan. '
        'Anda tetap dapat menambah alat baru, menghapus alat, mengubah jumlah unit, jam penggunaan, '
        'hari pemakaian, atau memilih daya yang berbeda. Seluruh perubahan tersebut akan dianggap '
        'sebagai kondisi terbaru dan dibandingkan dengan kondisi awal.</div>',
        unsafe_allow_html=True
    )

    st.markdown(
        '<div class="green-box"><b>Arahan skenario:</b> daftar alat tidak dikunci. '
        'Pengguna tetap dapat menambah alat untuk menguji perubahan kondisi rumah. '
        'Untuk mencoba penghematan, pengguna dapat mengurangi watt, jumlah unit, jam penggunaan, '
        'hari pemakaian, atau menghapus alat yang tidak diperlukan. Penambahan alat dapat menyebabkan '
        'konsumsi dan nilai IKE meningkat, dan hasil tersebut tetap akan ditampilkan secara objektif.</div>',
        unsafe_allow_html=True
    )

    a1, a2, a3, a4 = st.columns(4)
    with a1:
        st.metric("Konsumsi awal", f"{awal['total_kwh']:.2f} kWh")
    with a2:
        st.metric("Biaya awal", rupiah(awal["total_biaya"]))
    with a3:
        st.metric("IKE awal", f"{awal['ike']:.2f}")
    with a4:
        st.metric("Kategori awal", awal["kategori"])

    tombol_bandingkan, tombol_batal = st.columns([2, 1])

    with tombol_bandingkan:
        if st.button(
            "Bandingkan Kondisi Saat Ini dengan Kondisi Awal",
            use_container_width=True,
            type="primary"
        ):
            selisih_kwh = float(awal["total_kwh"] - total_kwh)
            selisih_biaya = float(awal["total_biaya"] - total_biaya)
            selisih_ike = float(awal["ike"] - ike)
            persen_hemat = (
                (selisih_kwh / awal["total_kwh"]) * 100
                if awal["total_kwh"] > 0 else 0.0
            )

            st.session_state.hasil_perbandingan = {
                "kwh_sekarang": float(total_kwh),
                "biaya_sekarang": float(total_biaya),
                "ike_sekarang": float(ike),
                "kategori_sekarang": cat,
                "selisih_kwh": selisih_kwh,
                "selisih_biaya": selisih_biaya,
                "selisih_ike": selisih_ike,
                "persen_hemat": float(persen_hemat),
            }

    with tombol_batal:
        if st.button("Hapus Kondisi Awal", use_container_width=True):
            st.session_state.kondisi_awal = None
            st.session_state.hasil_perbandingan = None
            st.session_state.pop("editor_daftar_alat", None)
            st.rerun()

    hasil = st.session_state.hasil_perbandingan
    if hasil is not None:
        st.markdown("#### Hasil Perbandingan")

        perbandingan_df = pd.DataFrame({
            "Indikator": ["Konsumsi listrik", "Estimasi biaya", "Nilai IKE", "Kategori"],
            "Kondisi awal": [
                f"{awal['total_kwh']:.2f} kWh/bulan",
                f"{rupiah(awal['total_biaya'])}/bulan",
                f"{awal['ike']:.2f} kWh/m²/bulan",
                awal["kategori"],
            ],
            "Kondisi saat ini": [
                f"{hasil['kwh_sekarang']:.2f} kWh/bulan",
                f"{rupiah(hasil['biaya_sekarang'])}/bulan",
                f"{hasil['ike_sekarang']:.2f} kWh/m²/bulan",
                hasil["kategori_sekarang"],
            ],
        })
        st.dataframe(perbandingan_df, use_container_width=True, hide_index=True)

        if hasil["selisih_kwh"] > 0.005:
            st.markdown(
                f'<div class="green-box">Perubahan yang dilakukan menurunkan konsumsi sebesar '
                f'<b>{hasil["selisih_kwh"]:.2f} kWh/bulan</b> '
                f'atau <b>{hasil["persen_hemat"]:.2f}%</b>. '
                f'Perkiraan biaya berkurang sebesar <b>{rupiah(hasil["selisih_biaya"])}/bulan</b>. '
                f'Nilai IKE turun dari {awal["ike"]:.2f} menjadi {hasil["ike_sekarang"]:.2f}.</div>',
                unsafe_allow_html=True
            )

            if hasil["kategori_sekarang"] == "TIDAK HEMAT":
                st.info(
                    "Konsumsi sudah menurun, tetapi hasil saat ini masih termasuk kategori tidak hemat. "
                    "Silakan lakukan penyesuaian kembali lalu tekan tombol bandingkan."
                )
            else:
                st.success(
                    "Hasil kondisi saat ini sudah berubah menjadi kategori hemat. "
                    "Pengguna tetap dapat mencoba perubahan lain jika ingin melihat skenario yang berbeda."
                )

        elif hasil["selisih_kwh"] < -0.005:
            kenaikan_kwh = abs(hasil["selisih_kwh"])
            persen_naik = abs(hasil["persen_hemat"])
            st.markdown(
                f'<div class="red-box">Kondisi saat ini justru meningkat sebesar '
                f'<b>{kenaikan_kwh:.2f} kWh/bulan</b> atau <b>{persen_naik:.2f}%</b> '
                f'dibandingkan kondisi awal. Periksa kembali jumlah alat, daya, jam, atau hari penggunaannya.</div>',
                unsafe_allow_html=True
            )
        else:
            st.info(
                "Belum terdapat perubahan konsumsi dibandingkan kondisi awal. "
                "Ubah salah satu data penggunaan alat, lalu lakukan perbandingan kembali."
            )

st.markdown('</div>', unsafe_allow_html=True)

# =========================================================
# VISUALISASI
# =========================================================
st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">6. Visualisasi IKE dan Alat Paling Boros</div>', unsafe_allow_html=True)
st.markdown('<div class="section-caption">Gauge menunjukkan posisi IKE, sedangkan grafik batang menunjukkan alat yang paling besar kontribusinya.</div>', unsafe_allow_html=True)

g1, g2 = st.columns([1, 1])

with g1:
    gauge_color = "#2EAD4F" if ike < 10 else "#E13A2D"
    fig = go.Figure(go.Indicator(
        mode="gauge+number",
        value=ike,
        number={"suffix": " kWh/m²/bulan", "font": {"size": 24}},
        title={"text": "Nilai IKE"},
        gauge={
            "shape": "angular",
            "axis": {"range": [0, 20], "tickwidth": 1},
            "bar": {"color": gauge_color},
            "bgcolor": "white",
            "borderwidth": 1,
            "bordercolor": "#DDEBDD",
            "steps": [
                {"range": [0, 5], "color": "#CFF0D5"},
                {"range": [5, 10], "color": "#8EDB9D"},
                {"range": [10, 15], "color": "#FFD56B"},
                {"range": [15, 20], "color": "#FF8A80"},
            ],
            "threshold": {
                "line": {"color": "#162317", "width": 4},
                "thickness": 0.85,
                "value": 10
            },
        }
    ))
    fig.update_layout(height=370, margin=dict(l=10, r=10, t=60, b=10), paper_bgcolor="rgba(0,0,0,0)")
    st.plotly_chart(fig, use_container_width=True)

    if ike < 10:
        st.markdown(f'<div class="green-box">Kategori: {cat}. {cat_note}</div>', unsafe_allow_html=True)
    else:
        st.markdown(f'<div class="red-box">Kategori: {cat}. {cat_note}</div>', unsafe_allow_html=True)

with g2:
    if df_selected.empty:
        st.info("Grafik akan tampil setelah alat ditambahkan.")
    else:
        chart_df = df_selected.sort_values("Konsumsi (kWh/Bulan)", ascending=True).tail(10)
        label_y = chart_df["Nama Alat"] + " — " + chart_df["Kapasitas/Satuan"].astype(str)
        fig_bar = go.Figure(go.Bar(
            x=chart_df["Konsumsi (kWh/Bulan)"],
            y=label_y,
            orientation="h",
            text=chart_df["Konsumsi (kWh/Bulan)"].map(lambda x: f"{x:.1f} kWh"),
            textposition="auto",
            marker_color="#2D7DCE"
        ))
        fig_bar.update_layout(
            height=370,
            title="10 Alat dengan Konsumsi Terbesar",
            xaxis_title="kWh/bulan",
            yaxis_title="",
            margin=dict(l=10, r=10, t=55, b=10),
            paper_bgcolor="rgba(0,0,0,0)",
            plot_bgcolor="#FFFFFF"
        )
        st.plotly_chart(fig_bar, use_container_width=True)

st.markdown('</div>', unsafe_allow_html=True)

# =========================================================
# SARAN EDUKATIF
# =========================================================
st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">7. Saran Penghematan</div>', unsafe_allow_html=True)
st.markdown('<div class="section-caption">Saran dibuat dari daftar alat yang dimasukkan dan alat dengan konsumsi terbesar.</div>', unsafe_allow_html=True)

for s in build_suggestions(df_selected, ike):
    st.markdown(f"- {s}")

st.markdown('</div>', unsafe_allow_html=True)

# =========================================================
# UNDUH
# =========================================================
st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">8. Unduh Hasil Simulasi</div>', unsafe_allow_html=True)
st.markdown('<div class="section-caption">Unduh hasil simulasi dalam format PDF sebagai dokumentasi.</div>', unsafe_allow_html=True)

summary = [
    "RINGKASAN HASIL SIMULASI IKE RUMAH TANGGA",
    f"Tanggal: {datetime.now().strftime('%d-%m-%Y %H:%M')}",
    "",
    f"Luas rumah/ruangan: {luas_rumah:.2f} m²",
    f"Tarif listrik: {rupiah(tarif_manual)} per kWh",
    f"Total konsumsi: {total_kwh:.2f} kWh/bulan",
    f"Estimasi biaya: {rupiah(total_biaya)} per bulan",
    f"Nilai IKE: {ike:.2f} kWh/m²/bulan",
    f"Kategori: {cat}",
    "",
    "Daftar alat listrik:"
]

if df_selected.empty:
    summary.append("- Belum ada alat yang ditambahkan.")
else:
    for _, row in df_selected.iterrows():
        summary.append(
            f"- {row['Nama Alat']} ({row['Kapasitas/Satuan']}) | "
            f"{row['Watt Dipakai']} W | faktor aktif {row['Faktor Aktif']} | "
            f"{row['Jumlah']} unit | {row['Jam/Hari']} jam/hari | "
            f"{row['Hari/Bulan']} hari/bulan | {row['Konsumsi (kWh/Bulan)']} kWh/bulan"
        )

summary.append("")
summary.append("Saran penghematan:")
for s in build_suggestions(df_selected, ike):
    summary.append(f"- {s}")

pdf_bytes = buat_pdf_ringkasan(
    df_selected,
    luas_rumah,
    tarif_manual,
    total_kwh,
    total_biaya,
    ike,
    cat,
    build_suggestions(df_selected, ike),
)

st.download_button(
    "Unduh Hasil Simulasi (PDF)",
    data=pdf_bytes,
    file_name="hasil_simulasi_penggunaan_listrik.pdf",
    mime="application/pdf",
    use_container_width=True
)

st.markdown('</div>', unsafe_allow_html=True)

# =========================================================
# ARAHAN SELESAI SIMULASI
# =========================================================
st.markdown('<div class="section">', unsafe_allow_html=True)
st.markdown('<div class="section-title">Selesai simulasi?</div>', unsafe_allow_html=True)
st.markdown(
    '<div class="section-caption">Tutup tab simulasi ini atau klik tab media interaktif yang masih terbuka di browser.</div>',
    unsafe_allow_html=True
)
st.markdown('</div>', unsafe_allow_html=True)

st.caption(
    "Catatan: Daya alat pada database merupakan estimasi untuk simulasi edukatif. "
    "Pengguna dapat memakai mode custom jika mengetahui watt aktual dari label alat. "
    "Jika alat tidak tersedia pada database, gunakan fitur Input alat manual."
)
