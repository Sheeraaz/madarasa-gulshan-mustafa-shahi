import streamlit as st
from datetime import datetime
import csv
import io

# =========================================================
# MADARASA GULSHAN-E-MUSTAFA SHAHI
# Version 1 - Web App
# =========================================================

st.set_page_config(
    page_title="मदरसा गुलशन-ए-मुस्तफा शाही",
    page_icon="🕌",
    layout="centered"
)

# -----------------------------
# DESIGN
# -----------------------------
st.markdown("""
<style>
.stApp {
    background: linear-gradient(135deg, #fffdf0 0%, #fff0f5 100%);
}

h1, h2, h3 {
    color: #0B6623 !important;
}

.stButton > button {
    background-color: #2E8B57;
    color: white;
    border-radius: 10px;
    border: none;
    font-weight: bold;
    width: 100%;
}

.stButton > button:hover {
    background-color: #246b45;
    color: white;
}

div[data-testid="stMetric"] {
    background-color: white;
    padding: 15px;
    border-radius: 12px;
    border: 1px solid #dddddd;
}

.header-box {
    background: linear-gradient(90deg, #0B6623, #2E8B57);
    padding: 20px;
    border-radius: 15px;
    color: white;
    text-align: center;
    margin-bottom: 20px;
}

.info-box {
    background-color: white;
    padding: 18px;
    border-radius: 12px;
    border: 1px solid #e5e5e5;
}
</style>
""", unsafe_allow_html=True)


# -----------------------------
# ADMIN PASSWORD
# -----------------------------
# अभी परीक्षण के लिए 1234 रखा गया है।
# बाद में इसे सुरक्षित Secrets में रखा जाएगा।

ADMIN_PASSWORD = "1234"


# -----------------------------
# SESSION DATA
# -----------------------------
if "logged_in" not in st.session_state:
    st.session_state.logged_in = False

if "ledger" not in st.session_state:
    st.session_state.ledger = []


# -----------------------------
# SIDEBAR LOGIN
# -----------------------------
st.sidebar.title("🔐 Admin Login")

if not st.session_state.logged_in:

    password = st.sidebar.text_input(
        "पासवर्ड दर्ज करें",
        type="password"
    )

    if st.sidebar.button("Login 🔓"):
        if password == ADMIN_PASSWORD:
            st.session_state.logged_in = True
            st.rerun()
        else:
            st.sidebar.error("गलत पासवर्ड!")

else:
    st.sidebar.success("Login सफल है ✅")

    if st.sidebar.button("Logout"):
        st.session_state.logged_in = False
        st.rerun()


# -----------------------------
# LOGIN REQUIRED
# -----------------------------
if not st.session_state.logged_in:

    st.markdown("""
    <div class="header-box">
        <h1 style="color:white !important;">
        🕌 मदरसा गुलशन-ए-मुस्तफा शाही
        </h1>
        <p>आधिकारिक डिजिटल पोर्टल</p>
    </div>
    """, unsafe_allow_html=True)

    st.info(
        "🔐 वेबसाइट का उपयोग करने के लिए बाईं तरफ Admin Login में पासवर्ड डालें।"
    )

    st.stop()


# -----------------------------
# LANGUAGE
# -----------------------------
lang = st.selectbox(
    "भाषा चुनें / Select Language / زبان منتخب کریں",
    ["हिंदी", "English", "اردو"]
)


# -----------------------------
# HEADER
# -----------------------------
if lang == "हिंदी":

    st.markdown("""
    <div class="header-box">
        <h1 style="color:white !important;">
        🕌 मदरसा गुलशन-ए-मुस्तफा शाही
        </h1>
        <p>तालीम • नमाज़ • सहयोग • पारदर्शी हिसाब-किताब</p>
    </div>
    """, unsafe_allow_html=True)

elif lang == "English":

    st.markdown("""
    <div class="header-box">
        <h1 style="color:white !important;">
        🕌 Madarasa Gulshan-E-Mustafa Shahi
        </h1>
        <p>Education • Prayer • Donation • Transparent Accounts</p>
    </div>
    """, unsafe_allow_html=True)

else:

    st.markdown("""
    <div class="header-box">
        <h1 style="color:white !important;">
        🕌 مدرسہ گلشنِ مصطفیٰ شاہی
        </h1>
        <p>تعلیم • نماز • تعاون • شفاف حساب کتاب</p>
    </div>
    """, unsafe_allow_html=True)


# -----------------------------
# TABS
# -----------------------------
tab1, tab2, tab3, tab4 = st.tabs([
    "📋 मुख्य मेनू",
    "💰 QR और दान",
    "📊 हिसाब-किताब",
    "⚙️ सेटिंग्स"
])


# =========================================================
# TAB 1
# =========================================================

with tab1:

    st.header("📋 मुख्य जानकारी")

    col1, col2 = st.columns(2)

    with col1:
        st.markdown("""
        <div class="info-box">
        <h3>🕌 नमाज़</h3>
        फजर<br>
        ज़ुहर<br>
        असर<br>
        मगरिब<br>
        इशा
        </div>
        """, unsafe_allow_html=True)

    with col2:
        st.markdown("""
        <div class="info-box">
        <h3>📚 तालीम</h3>
        बच्चों की दीनी तालीम<br>
        कुरआन शिक्षा<br>
        नैतिक शिक्षा<br>
        दुनियावी शिक्षा
        </div>
        """, unsafe_allow_html=True)

    st.write("")

    st.success(
        "मदरसे की शिक्षा और आवश्यक कार्यों में सहयोग करने के लिए आपका धन्यवाद।"
    )


# =========================================================
# TAB 2 - DONATION
# =========================================================

with tab2:

    st.header("💰 डिजिटल दान / सहयोग")

    st.write(
        "नीचे दिए गए QR Code के माध्यम से मदरसे में सहयोग किया जा सकता है।"
    )

    # =====================================================
    # IMPORTANT:
    # यहाँ YOUR_UPI_ID को असली UPI ID से बदलना है।
    # उदाहरण: madarsa123@upi
    # =====================================================

    UPI_ID = "YOUR_UPI_ID@upi"
    MADARASA_NAME = "Madarasa Gulshan-E-Mustafa Shahi"

    upi_link = (
        f"upi://pay?pa={UPI_ID}"
        f"&pn={MADARASA_NAME}"
        f"&cu=INR"
    )

    qr_url = (
        "https://api.qrserver.com/v1/create-qr-code/"
        "?size=300x300&data="
        + upi_link
    )

    st.image(
        qr_url,
        caption="📱 Scan करके सहयोग करें",
        width=300
    )

    st.code(UPI_ID)

    st.info(
        "⚠️ ऊपर YOUR_UPI_ID@upi को अपनी वास्तविक UPI ID से बदलना होगा।"
    )


# =========================================================
# TAB 3 - LEDGER
# =========================================================

with tab3:

    st.header("📊 हिसाब-किताब / Ledger")

    total_income = sum(
        item["amount"]
        for item in st.session_state.ledger
        if item["type"] == "जमा"
    )

    total_expense = sum(
        item["amount"]
        for item in st.session_state.ledger
        if item["type"] == "खर्च"
    )

    balance = total_income - total_expense

    col1, col2, col3 = st.columns(3)

    col1.metric(
        "💰 कुल जमा",
        f"₹ {total_income:,.2f}"
    )

    col2.metric(
        "💸 कुल खर्च",
        f"₹ {total_expense:,.2f}"
    )

    col3.metric(
        "📊 बाकी",
        f"₹ {balance:,.2f}"
    )

    st.divider()

    st.subheader("➕ नई एंट्री जोड़ें")

    entry_type = st.selectbox(
        "एंट्री का प्रकार",
        ["जमा", "खर्च"]
    )

    amount = st.number_input(
        "राशि (₹)",
        min_value=0.0,
        step=100.0
    )

    description = st.text_input(
        "विवरण",
        placeholder="जैसे: दान / बिजली बिल / राशन"
    )

    if st.button("💾 एंट्री सेव करें"):

        if amount <= 0:
            st.error("कृपया सही राशि डालें।")

        elif description.strip() == "":
            st.error("कृपया विवरण लिखें।")

        else:

            st.session_state.ledger.append({
                "date": datetime.now().strftime("%d-%m-%Y %H:%M"),
                "type": entry_type,
                "amount": amount,
                "description": description
            })

            st.success("एंट्री सफलतापूर्वक सेव हो गई ✅")
            st.rerun()

    st.divider()

    st.subheader("📋 लेन-देन की सूची")

    if len(st.session_state.ledger) == 0:

        st.info("अभी कोई लेन-देन दर्ज नहीं है।")

    else:

        for item in reversed(st.session_state.ledger):

            if item["type"] == "जमा":
                symbol = "🟢"
            else:
                symbol = "🔴"

            st.write(
                f"{symbol} **{item['type']}** | "
                f"₹ {item['amount']:,.2f} | "
                f"{item['description']} | "
                f"{item['date']}"
            )

        st.divider()

        # CSV DOWNLOAD
        output = io.StringIO()

        writer = csv.writer(output)

        writer.writerow([
            "Date",
            "Type",
            "Amount",
            "Description"
        ])

        for item in st.session_state.ledger:
            writer.writerow([
                item["date"],
                item["type"],
                item["amount"],
                item["description"]
            ])

        st.download_button(
            label="⬇️ हिसाब CSV में डाउनलोड करें",
            data=output.getvalue(),
            file_name="madarasa_ledger.csv",
            mime="text/csv"
        )


# =========================================================
# TAB 4 - SETTINGS
# =========================================================

with tab4:

    st.header("⚙️ Admin Control Panel")

    st.write("यह मदरसे के डिजिटल पोर्टल का Admin क्षेत्र है।")

    st.info(
        "अभी Version 1 में Login, Donation QR और Ledger की सुविधा उपलब्ध है।"
    )

    st.write("### 🕌 Madarasa Gulshan-E-Mustafa Shahi")

    st.write(
        "आगे के संस्करण में विद्यार्थियों, फीस, रसीद, "
        "मासिक रिपोर्ट और स्थायी Database जोड़ा जा सकता है।"
    )

    st.caption(
        "Version 1.0"
    )
