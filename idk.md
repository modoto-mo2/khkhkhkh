حل شد داداش! هر ۳ مورد دقیقاً پیاده‌سازی شدند:

---

### 🛠 جزئیات ۳ تغییر جدید:

1. **جلوگیری از برگشت بسته‌های استارز بعد از ری‌استارت:**
   * **علت باگ:** کد شرط گذاشته بود که اگر تعداد بسته‌ها صفر شد، دوباره بسته‌های پیش‌فرض ارزان رو از نو بسازه!
   * **حل شد:** سیستم سیدینگ یک‌بار مصرف شد؛ از این به بعد وقتی بسته‌ای رو حذف کنی، با ۱۰۰ بار ری‌استارت هم دیگه هرگز بسته‌های پیش‌فرض برنمی‌گردن.

2. **اعمال کامل استایل‌ها (`style="primary"`, `style="success"`, `style="danger"`) روی تمام دکمه‌های ادمین:**
   * تمام دکمه‌های منوی اصلی ادمین، زیرمنوها، تنظیمات، لیست‌ها و عملیات‌ها استایل رنگی گرفتند.

3. **اضافه شدن قابلیت پروندن / لغو احراز هویت (KYC) کاربر:**
   * دکمه قرمز رنگ **`🪪 لغو / ابطال KYC کاربر`** به پنل ادمین اضافه شد.
   * کافیه آیدی عددی یا یوزرنیم طرف رو بدی ➔ احراز هویت و کارت تایید شده‌ش در جا باطل و صفر میشه و به پیویش پیام میره که احراز شما لغو شد و باید مجدداً احراز کنه!

---

### 🚀 دستور لینوکسی جایگزینی (اجرا در ترمینال سرور):

این دستور رو کامل داخل سرورت پیست و اجرا کن:

```bash
cat << 'EOF' > database.py
import aiosqlite, re
from typing import Optional, Dict, Any, List
from config import config

CACHE_EMOJIS = {}
REVERSE_EMOJIS = {}

ALL_DIGITS_STR = "0123456789۰۱۲۳۴۵۶۷۸۹٠١٢٣٤٥٦٧٨٩𝟘𝟙𝟚𝟛𝟜𝟝𝟞𝟟𝟠𝟡⁰¹²³⁴⁵⁶⁷⁸⁹₀₁₂₃₄₅₆₇₈₉𝟎𝟏𝟐𝟑𝟒𝟓𝟔𝟕𝟖𝟗𝟬𝟭𝟮𝟯𝟰𝟱𝟲𝟳𝟴𝟵𝟢𝟣𝟤𝟥𝟦𝟧𝟨𝟩𝟪𝟫𝟷𝟸𝟹𝟺𝟻𝟼𝟽𝟾𝟿⓿➊➋➌➍➎➏➐➑➒⓪①②③④⑤⑥⑦⑧⑨🄋➀➁➂➃➄➅➆➇➈⒪⑴⑵⑶⑷⑸⑹⑺⑻⑼🄀⒈⒉⒊⒋⒌⒍⒎⒏⒐🄌➊➋➌➍➎➏➐➑➒０１２３۴۵۶۷۸۹🯰🯱🯲𝯳𝯴𝯵𝯶𝯷𝯸𝯹ⅠⅡⅢⅣⅤⅥⅦⅧⅨ❶❷❸❹❺❻❼❽❾⠚⠁⠃⠉⠙⠑⠋⠛⠓⠊०१२३४५६७८९০১২৩৪৫৬৭৮৯๐๑๒๓๔๕๖๗๘๙༠༡༢༣༤༥༦༧༨༩០១២៣៤۵۶۷۸۹၀၁၂၃၄၅၆၇၈၉੦੧੨੩੪੫੬੭੮੯೦೧೨೩೪೫೬೭೮೯൦൧൨൩൪൫൬൭൮൯௦௧௨௩௪௫௬௭௮௯౦౧౨౩౪౫౬౭౮౯૦૧૨૩૪૫૬૭૮૯୦୧୨୩୪୫୬୭୮୯෦෧෨෩෪෫෬෭෮෯໐໑໒໓໔໕໖໗໘໙"
BRACKET_SYMBOLS = r"﹝﹞❲❳‹›「」【】〖〗⟦⟧⦅⦆﹤﹥⟮⟯⟬⟭❨❩❮❯«»𓆩𓆪⸢⸥⌜⌟☽☾✦✧⌁亗༒⊶⊷༺༻⧉⟡|•\(\)\[\]"

def clean_time_from_string(text: str) -> str:
    if not text: return ""
    pattern = rf'(?:[{BRACKET_SYMBOLS}]\s*)?[{re.escape(ALL_DIGITS_STR)}]{{1,2}}[:：⠒][{re.escape(ALL_DIGITS_STR)}]{{2}}(?:\s*[{BRACKET_SYMBOLS}])?'
    cleaned = re.sub(pattern, '', text)
    cleaned = re.sub(r'\s{2,}', ' ', cleaned)
    return cleaned.strip()

def normalize_template_clock(tmpl: str) -> str:
    if not tmpl or "{clock}" not in tmpl:
        return f"{tmpl} {{clock}}".strip()
    pattern = rf'[{BRACKET_SYMBOLS}]\s*\{{clock\}}\s*[{BRACKET_SYMBOLS}]|[{BRACKET_SYMBOLS}]\s*\{{clock\}}|\{{clock\}}\s*[{BRACKET_SYMBOLS}]'
    normalized = re.sub(pattern, '{clock}', tmpl)
    return normalized.strip()

def clean_number(text: str) -> int:
    p, a = "۰۱۲۳۴۵۶۷۸۹", "٠١٢٣٤٥٦٧۸۹"
    res = "".join([c if c.isdigit() else str(p.index(c)) if c in p else str(a.index(c)) if c in a else "" for c in str(text)])
    return int(res) if res else 0

def is_cancel_message(text: str) -> bool:
    if not text: return False
    t = text.strip().lower()
    return "انصراف" in t or "cancel" in t or "❌" in t

def clean_all_tags(text: str) -> str:
    return re.sub(r"<tg-emoji[^>]*>(.*?)</tg-emoji>", r"\1", text) if text else text

async def add_col(db, table, col, col_type):
    c = await db.execute(f"PRAGMA table_info({table})")
    cols = [r[1] for r in await c.fetchall()]
    if col not in cols: await db.execute(f"ALTER TABLE {table} ADD COLUMN {col} {col_type}")

async def init_db():
    global CACHE_EMOJIS, REVERSE_EMOJIS
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("CREATE TABLE IF NOT EXISTS settings (key TEXT PRIMARY KEY, value TEXT)")
        await db.execute("CREATE TABLE IF NOT EXISTS admins (user_id INTEGER PRIMARY KEY, added_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)")
        await db.execute("""CREATE TABLE IF NOT EXISTS users (
            user_id INTEGER PRIMARY KEY, username TEXT, full_name TEXT, is_verified INTEGER DEFAULT 0,
            kyc_status INTEGER DEFAULT 0, verified_card TEXT DEFAULT NULL, balance INTEGER DEFAULT 0,
            referrer_id INTEGER DEFAULT NULL, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)""")
        await db.execute("""CREATE TABLE IF NOT EXISTS discount_coupons (
            code TEXT PRIMARY KEY, discount_percent INTEGER DEFAULT 0, discount_amount INTEGER DEFAULT 0,
            max_uses INTEGER, used_count INTEGER DEFAULT 0, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)""")
        await db.execute("CREATE TABLE IF NOT EXISTS coupon_redemptions (code TEXT, user_id INTEGER, redeemed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, PRIMARY KEY (code, user_id))")
        await db.execute("""CREATE TABLE IF NOT EXISTS orders (
            id INTEGER PRIMARY KEY AUTOINCREMENT, user_id INTEGER, service_name TEXT,
            recipient_info TEXT, amount_paid INTEGER, status TEXT DEFAULT 'pending', created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)""")
        await db.execute("CREATE TABLE IF NOT EXISTS emoji_mappings (normal_emoji TEXT PRIMARY KEY, custom_emoji_id TEXT)")
        await db.execute("""CREATE TABLE IF NOT EXISTS custom_gifts (
            id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, stars_cost INTEGER DEFAULT 0,
            price INTEGER, custom_emoji_id TEXT DEFAULT '')""")
        await db.execute("""CREATE TABLE IF NOT EXISTS stars_packages (
            id INTEGER PRIMARY KEY AUTOINCREMENT, btn_title TEXT, stars_amount INTEGER, price INTEGER)""")
        await db.execute("""CREATE TABLE IF NOT EXISTS deposit_receipts (
            id INTEGER PRIMARY KEY AUTOINCREMENT, user_id INTEGER, amount INTEGER,
            photo_file_id TEXT, status TEXT DEFAULT 'pending', handled_by TEXT DEFAULT NULL,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)""")
        await db.execute("""CREATE TABLE IF NOT EXISTS kyc_submissions (
            id INTEGER PRIMARY KEY AUTOINCREMENT, user_id INTEGER, card_number TEXT,
            photo_file_id TEXT, status TEXT DEFAULT 'pending', handled_by TEXT DEFAULT NULL,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)""")
        await db.execute("""CREATE TABLE IF NOT EXISTS required_channels (
            id INTEGER PRIMARY KEY AUTOINCREMENT, channel_id TEXT UNIQUE,
            channel_link TEXT, channel_title TEXT DEFAULT 'کانال')""")
        await db.execute("""CREATE TABLE IF NOT EXISTS self_bots (
            user_id INTEGER PRIMARY KEY, phone_number TEXT, session_string TEXT,
            is_active INTEGER DEFAULT 1, clock_name INTEGER DEFAULT 0,
            clock_bio INTEGER DEFAULT 0, clock_font TEXT DEFAULT 'persian', clock_name_pos TEXT DEFAULT 'end',
            afk_status INTEGER DEFAULT 0, afk_reason TEXT DEFAULT '', auto_format_mode TEXT DEFAULT 'none',
            name_template TEXT DEFAULT 'User {clock}', original_name TEXT DEFAULT '', original_bio TEXT DEFAULT '',
            save_ttl INTEGER DEFAULT 1, anti_delete INTEGER DEFAULT 1, anti_edit INTEGER DEFAULT 1, save_pv INTEGER DEFAULT 0,
            track_pfp INTEGER DEFAULT 1,
            dst_ttl TEXT DEFAULT 'me', dst_del TEXT DEFAULT 'me', dst_edit TEXT DEFAULT 'me', dst_pv TEXT DEFAULT 'me', dst_pfp TEXT DEFAULT 'me',
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)""")
        await db.execute("""CREATE TABLE IF NOT EXISTS support_tickets (
            id INTEGER PRIMARY KEY AUTOINCREMENT, user_id INTEGER, message_text TEXT,
            status TEXT DEFAULT 'open', answered_by TEXT DEFAULT NULL, created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)""")

        for c, t in [("is_verified", "INTEGER DEFAULT 0"), ("balance", "INTEGER DEFAULT 0"), ("referrer_id", "INTEGER DEFAULT NULL"), ("kyc_status", "INTEGER DEFAULT 0"), ("verified_card", "TEXT DEFAULT NULL")]:
            await add_col(db, "users", c, t)

        self_cols = [
            ("name_template", "TEXT DEFAULT 'User {clock}'"), ("original_name", "TEXT DEFAULT ''"),
            ("original_bio", "TEXT DEFAULT ''"), ("clock_name_pos", "TEXT DEFAULT 'end'"),
            ("afk_status", "INTEGER DEFAULT 0"), ("afk_reason", "TEXT DEFAULT ''"),
            ("auto_format_mode", "TEXT DEFAULT 'none'"), ("save_ttl", "INTEGER DEFAULT 1"),
            ("anti_delete", "INTEGER DEFAULT 1"), ("anti_edit", "INTEGER DEFAULT 1"),
            ("save_pv", "INTEGER DEFAULT 0"), ("track_pfp", "INTEGER DEFAULT 1"),
            ("dst_ttl", "TEXT DEFAULT 'me'"), ("dst_del", "TEXT DEFAULT 'me'"),
            ("dst_edit", "TEXT DEFAULT 'me'"), ("dst_pv", "TEXT DEFAULT 'me'"), ("dst_pfp", "TEXT DEFAULT 'me'")
        ]
        for c, t in self_cols:
            await add_col(db, "self_bots", c, t)

        await db.execute("INSERT OR IGNORE INTO admins (user_id) VALUES (?)", (config.ADMIN_ID,))
        
        defaults = {
            "card_number": "6037-9900-0000-0000", "card_holder": "نام صاحب حساب",
            "bot_status": "active",
            "welcome_text": "سلام {name} عزیز، به ربات خوش آمدید!\n\nاز منوی زیر استفاده کنید:",
            "kyc_guide_text": "🪪 <b>بخش احراز هویت</b>\n\n✔️ <b>برای استفاده از این روش پرداخت، یک بار باید هویتتان توسط ادمین تأیید شود.</b>\n\n⁉️ <b>مدارک مورد نیاز:</b>\n\n• عکس کارت بانکی به نام خودتان (CVV2 و تاریخ انقضا را بپوشانید)\n• عکس کارت در کنار دست‌نوشته:\n<i>«جهت خرید خدمات از این ربات از کارت [شماره کارت] احراز هویت انجام می‌شود.»</i>\n\n💡 <i>این فرآیند فقط یک‌بار انجام می‌شود.</i>",
            "guide_text": "📚 <b>راهنمای جامع بخش‌های ربات:</b>\n\n⭐️ <b>۱. فروشگاه خدمات:</b>\nخرید استارز، پرمیوم، تون و گیفت‌ها با شارژ کیف پول.\n\n🚀 <b>۲. سلف‌بات ابری PuLaSeLf:</b>\nفعال‌سازی سلف اختصاصی روی اکانت شما.\n\n👥 <b>۳. کسب درآمد و زیرمجموعه‌گیری:</b>\nبا لینک دعوت خود، به ازای هر ورود پاداش نقدی بگیرید.",
            "welcome_bonus": "10000", "referral_bonus": "5000", "min_deposit": "100000", "max_deposit": "5000000",
            "required_channel": "", "channel_link": "", "emoji_msg_cost": "0", "btn_stars": "⭐️ خرید استارز",
            "btn_premium": "💎 خرید پرمیوم", "btn_ton": "🪙 خرید تون", "btn_gifts": "🎁 گیفتهای تلگرام",
            "btn_wallet": "💰 کیف پول", "btn_ref": "👥 زیرمجموعهگیری", "btn_guide": "📖 راهنما", "btn_support": "📞 پشتیبانی",
            "btn_self": "🚀 مدیریت سلف", "self_hourly_price": "600",
            "stars_base_50_price": "75000", "stars_min_buy": "50", "stars_max_buy": "25000",
            "prem_3m": "360000", "prem_6m": "680000", "prem_12m": "1250000", "ton_unit_price": "330423",
            "sticker_welcome": "", "sticker_success": "", "sticker_kyc": "", "sticker_wallet": ""
        }
        for k, v in defaults.items(): await db.execute("INSERT OR IGNORE INTO settings (key, value) VALUES (?, ?)", (k, v))

        default_emojis = [
            ("💎", "5382103597371946894"), ("⭐", "5976828720487337373"), ("⭐️", "5976828720487337373"),
            ("🪙", "5431671917789498246"), ("🎁", "5373147814986790938"), ("💰", "5314504236132747481"),
            ("👥", "5420323339723881652"), ("📖", "5769547529993588669"), ("📞", "5830326445422940546"),
            ("✅", "5830326445422940546"), ("❌", "5832353674281620438"), ("⚠️", "5420323339723881652")
        ]
        for n, cid in default_emojis: await db.execute("INSERT OR IGNORE INTO emoji_mappings (normal_emoji, custom_emoji_id) VALUES (?, ?)", (n, cid))

        # ایجاد فقط یکبار در اولین ساخت دیتابیس (تا اگر ادمین پاک کرد دوباره برنگردد!)
        is_seeded = await (await db.execute("SELECT value FROM settings WHERE key = 'db_init_seeded'")).fetchone()
        if not is_seeded:
            for g_name, g_stars, g_price in [("خرس تدی 🧸", 100, 150000), ("قلب سرخ ❤️", 50, 100000), ("الماس درخشان 💎", 250, 250000)]:
                await db.execute("INSERT INTO custom_gifts (name, stars_cost, price) VALUES (?, ?, ?)", (g_name, g_stars, g_price))

            for title, amt, prc in [("⭐️ ۵۰ استارز", 50, 75000), ("⭐️ ۱۰۰ استارز", 100, 145000), ("⭐️ ۲۵۰ استارز", 250, 355000), ("⭐️ ۵۰۰ استارز", 500, 695000), ("⭐️ ۱۰۰۰ استارز", 1000, 1380000)]:
                await db.execute("INSERT INTO stars_packages (btn_title, stars_amount, price) VALUES (?, ?, ?)", (title, amt, prc))
            
            await db.execute("INSERT INTO settings (key, value) VALUES ('db_init_seeded', '1')")

        await db.commit()
    await load_emoji_cache()

async def load_emoji_cache():
    global CACHE_EMOJIS, REVERSE_EMOJIS
    async with aiosqlite.connect(config.DB_NAME) as db:
        rows = await (await db.execute("SELECT normal_emoji, custom_emoji_id FROM emoji_mappings")).fetchall()
        CACHE_EMOJIS = {r[0].strip(): str(r[1]).strip() for r in rows if r[0] and r[1] and str(r[1]).strip().isdigit()}
        REVERSE_EMOJIS = {str(r[1]).strip(): r[0].strip() for r in rows if r[0] and r[1]}

def transform_text_emojis(text: str) -> str:
    if not text or not isinstance(text, str):
        return text

    parts = re.split(r"(<code[^>]*>.*?</code>|<pre[^>]*>.*?</pre>|<tg-emoji[^>]*>.*?</tg-emoji>|<[^>]+>)", text, flags=re.DOTALL | re.IGNORECASE)

    new_parts = []
    for part in parts:
        if not part:
            continue
        if (part.startswith("<") and part.endswith(">")) or part.lower().startswith("<code") or part.lower().startswith("<pre"):
            new_parts.append(part)
        else:
            def repl_bracket(match):
                eid = match.group(1).strip()
                fallback = REVERSE_EMOJIS.get(eid, "💎")
                return f'<tg-emoji emoji-id="{eid}">{fallback}</tg-emoji>'

            sub_res = re.sub(r"\[\s*(\d{5,25})\s*\]", repl_bracket, part)

            if CACHE_EMOJIS:
                sorted_emojis = sorted(CACHE_EMOJIS.keys(), key=len, reverse=True)
                pattern = re.compile("|".join(re.escape(e) for e in sorted_emojis if e))

                def repl_emo(m):
                    emo = m.group(0)
                    cid = CACHE_EMOJIS.get(emo) or CACHE_EMOJIS.get(emo.replace("\ufe0f", ""))
                    return f'<tg-emoji emoji-id="{cid}">{emo}</tg-emoji>' if cid else emo

                sub_res = pattern.sub(repl_emo, sub_res)

            new_parts.append(sub_res)

    return "".join(new_parts)

def render_text(t: str) -> str: return transform_text_emojis(t)

async def is_admin(uid: int) -> bool:
    if uid == config.ADMIN_ID: return True
    async with aiosqlite.connect(config.DB_NAME) as db:
        return bool(await (await db.execute("SELECT 1 FROM admins WHERE user_id = ?", (uid,))).fetchone())

async def add_admin(uid: int):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("INSERT OR IGNORE INTO admins (user_id) VALUES (?)", (uid,)); await db.commit()

async def remove_admin(uid: int):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("DELETE FROM admins WHERE user_id = ?", (uid,)); await db.commit()

async def get_all_admins() -> List[int]:
    async with aiosqlite.connect(config.DB_NAME) as db:
        rows = await (await db.execute("SELECT user_id FROM admins")).fetchall()
        admins = [config.ADMIN_ID]
        for r in rows:
            if r[0] not in admins: admins.append(r[0])
        return admins

async def get_emoji_map(): return CACHE_EMOJIS
async def set_emoji_map(n, cid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("INSERT OR REPLACE INTO emoji_mappings (normal_emoji, custom_emoji_id) VALUES (?, ?)", (n.strip(), cid.strip())); await db.commit()
    await load_emoji_cache()

async def delete_emoji_map(n):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("DELETE FROM emoji_mappings WHERE normal_emoji = ?", (n.strip(),)); await db.commit()
    await load_emoji_cache()

async def get_setting(key: str, default: str = "") -> str:
    async with aiosqlite.connect(config.DB_NAME) as db:
        row = await (await db.execute("SELECT value FROM settings WHERE key = ?", (key,))).fetchone()
        return row[0] if row else default

async def get_int_setting(key: str, default: int = 0) -> int:
    v = await get_setting(key, str(default))
    n = clean_number(v)
    return n if n else default

async def set_setting(k, v):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("INSERT OR REPLACE INTO settings (key, value) VALUES (?, ?)", (k, str(v))); await db.commit()

async def send_bot_sticker(bot, chat_id: int, key: str):
    stk = await get_setting(key, "")
    if stk:
        try: await bot.send_sticker(chat_id=chat_id, sticker=stk)
        except Exception: pass

async def get_or_create_user(uid: int, username=None, full_name="", referrer_id=None):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        u = await (await db.execute("SELECT * FROM users WHERE user_id = ?", (uid,))).fetchone()
        if not u:
            ref = referrer_id if (referrer_id and referrer_id != uid) else None
            await db.execute("INSERT INTO users (user_id, username, full_name, referrer_id, is_verified, balance, kyc_status) VALUES (?, ?, ?, ?, 0, 0, 0)", (uid, username, full_name, ref))
            await db.commit()
            u = await (await db.execute("SELECT * FROM users WHERE user_id = ?", (uid,))).fetchone()
        return dict(u) if u else {"user_id": uid, "is_verified": 0, "balance": 0, "kyc_status": 0}

async def get_user(uid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        u = await (await db.execute("SELECT * FROM users WHERE user_id = ?", (uid,))).fetchone()
        return dict(u) if u else None

async def find_user(identifier: str):
    raw = str(identifier).strip().lstrip("@")
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        if raw.isdigit() or (raw.startswith("-") and raw[1:].isdigit()):
            u = await (await db.execute("SELECT * FROM users WHERE user_id = ?", (int(raw),))).fetchone()
            if u: return dict(u)
        u = await (await db.execute("SELECT * FROM users WHERE LOWER(username) = LOWER(?)", (raw,))).fetchone()
        return dict(u) if u else None

async def set_user_verified(uid, s=True):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE users SET is_verified = ? WHERE user_id = ?", (1 if s else 0, uid)); await db.commit()

async def set_user_kyc(uid, status: int, card=None):
    async with aiosqlite.connect(config.DB_NAME) as db:
        is_v = 1 if status == 2 else 0
        if card: await db.execute("UPDATE users SET kyc_status = ?, is_verified = ?, verified_card = ? WHERE user_id = ?", (status, is_v, str(card).strip(), uid))
        else: await db.execute("UPDATE users SET kyc_status = ?, is_verified = ? WHERE user_id = ?", (status, is_v, uid))
        await db.commit()

# باطل کردن و پروندن احراز هویت کاربر
async def revoke_user_kyc(uid: int):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE users SET kyc_status = 0, is_verified = 0, verified_card = NULL WHERE user_id = ?", (uid,))
        await db.commit()

async def update_balance(uid, amt):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE users SET balance = balance + ? WHERE user_id = ?", (amt, uid)); await db.commit()

async def get_all_user_ids():
    async with aiosqlite.connect(config.DB_NAME) as db:
        return [r[0] for r in await (await db.execute("SELECT user_id FROM users")).fetchall()]

async def get_referrals_count(uid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        return (await (await db.execute("SELECT COUNT(*) FROM users WHERE referrer_id = ? AND is_verified = 1", (uid,))).fetchone())[0]

async def create_coupon(code, pct, amt, max_u):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("INSERT OR REPLACE INTO discount_coupons (code, discount_percent, discount_amount, max_uses, used_count) VALUES (?, ?, ?, ?, 0)", (code.upper(), pct, amt, max_u)); await db.commit()

async def get_all_coupons():
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        return [dict(r) for r in await (await db.execute("SELECT * FROM discount_coupons ORDER BY created_at DESC")).fetchall()]

async def delete_coupon(code):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("DELETE FROM discount_coupons WHERE code = ?", (code.upper(),)); await db.commit()

async def validate_coupon(code, uid, base_p):
    code = code.upper()
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        c = await (await db.execute("SELECT * FROM discount_coupons WHERE code = ?", (code,))).fetchone()
        if not c: return False, "❌ کوپن تخفیف نامعتبر است.", base_p
        c = dict(c)
        if c["used_count"] >= c["max_uses"]: return False, "⚠️ ظرفیت استفاده از این کوپن تمام شده است.", base_p
        if await (await db.execute("SELECT * FROM coupon_redemptions WHERE code = ? AND user_id = ?", (code, uid))).fetchone():
            return False, "⚠️ شما قبلاً از این کوپن استفاده کردهاید.", base_p
        disc = int((base_p * c["discount_percent"]) / 100) if c["discount_percent"] > 0 else c["discount_amount"]
        return True, f"🎟 تخفیف <b>{disc:,} تومان</b> اعمال شد.", max(0, base_p - disc)

async def apply_coupon_use(code, uid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("INSERT INTO coupon_redemptions (code, user_id) VALUES (?, ?)", (code.upper(), uid))
        await db.execute("UPDATE discount_coupons SET used_count = used_count + 1 WHERE code = ?", (code.upper(),)); await db.commit()

async def create_order(uid, s_name, recip, amt):
    async with aiosqlite.connect(config.DB_NAME) as db:
        c = await db.execute("INSERT INTO orders (user_id, service_name, recipient_info, amount_paid) VALUES (?, ?, ?, ?)", (uid, s_name, recip, amt))
        await db.commit(); return c.lastrowid

async def update_order_status(oid, st):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE orders SET status = ? WHERE rowid = ?", (st, oid)); await db.commit()

async def get_order(oid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        r = await (await db.execute("SELECT * FROM orders WHERE rowid = ?", (oid,))).fetchone()
        return dict(r) if r else None

async def create_deposit_rec(uid, amt, photo_id):
    async with aiosqlite.connect(config.DB_NAME) as db:
        c = await db.execute("INSERT INTO deposit_receipts (user_id, amount, photo_file_id) VALUES (?, ?, ?)", (uid, amt, photo_id))
        await db.commit(); return c.lastrowid

async def get_deposit_rec(dep_id):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        r = await (await db.execute("SELECT * FROM deposit_receipts WHERE id = ?", (dep_id,))).fetchone()
        return dict(r) if r else None

async def resolve_deposit_rec(dep_id, status, admin_name):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE deposit_receipts SET status = ?, handled_by = ? WHERE id = ?", (status, admin_name, dep_id))
        await db.commit()

async def create_kyc_sub(uid, card, photo_id):
    async with aiosqlite.connect(config.DB_NAME) as db:
        c = await db.execute("INSERT INTO kyc_submissions (user_id, card_number, photo_file_id) VALUES (?, ?, ?)", (uid, card, photo_id))
        await db.commit(); return c.lastrowid

async def get_kyc_sub(sub_id):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        r = await (await db.execute("SELECT * FROM kyc_submissions WHERE id = ?", (sub_id,))).fetchone()
        return dict(r) if r else None

async def resolve_kyc_sub(sub_id, status, admin_name):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE kyc_submissions SET status = ?, handled_by = ? WHERE id = ?", (status, admin_name, sub_id))
        await db.commit()

async def create_support_ticket(uid: int, text: str):
    async with aiosqlite.connect(config.DB_NAME) as db:
        c = await db.execute("INSERT INTO support_tickets (user_id, message_text) VALUES (?, ?)", (uid, text))
        await db.commit(); return c.lastrowid

async def get_support_ticket(ticket_id: int):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        r = await (await db.execute("SELECT * FROM support_tickets WHERE id = ?", (ticket_id,))).fetchone()
        return dict(r) if r else None

async def resolve_support_ticket(ticket_id: int, admin_name: str):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE support_tickets SET status = 'answered', answered_by = ? WHERE id = ? AND status = 'open'", (admin_name, ticket_id))
        await db.commit()

async def save_self_bot(uid: int, phone: str, session_str: str):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("""INSERT OR REPLACE INTO self_bots (user_id, phone_number, session_string, is_active)
                            VALUES (?, ?, ?, 1)""", (uid, phone, session_str))
        await db.commit()

async def get_self_bot(uid: int):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        r = await (await db.execute("SELECT * FROM self_bots WHERE user_id = ?", (uid,))).fetchone()
        return dict(r) if r else None

async def get_all_active_self_bots():
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        rows = await (await db.execute("SELECT * FROM self_bots WHERE is_active = 1")).fetchall()
        return [dict(r) for r in rows]

async def get_all_self_bots_admin():
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        rows = await (await db.execute("SELECT * FROM self_bots ORDER BY created_at DESC")).fetchall()
        return [dict(r) for r in rows]

async def update_self_bot(uid: int, **kwargs):
    if not kwargs: return
    sets = ", ".join([f"{k} = ?" for k in kwargs.keys()])
    vals = list(kwargs.values()) + [uid]
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute(f"UPDATE self_bots SET {sets} WHERE user_id = ?", vals)
        await db.commit()

async def delete_self_bot(uid: int):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("DELETE FROM self_bots WHERE user_id = ?", (uid,))
        await db.commit()

async def get_all_stars_packages():
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        return [dict(r) for r in await (await db.execute("SELECT * FROM stars_packages ORDER BY stars_amount ASC")).fetchall()]

async def get_stars_package(pid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        r = await (await db.execute("SELECT * FROM stars_packages WHERE id = ?", (pid,))).fetchone()
        return dict(r) if r else None

async def add_stars_package(title, amt, prc):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("INSERT INTO stars_packages (btn_title, stars_amount, price) VALUES (?, ?, ?)", (title, amt, prc)); await db.commit()

async def update_stars_package_price(pid, prc):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE stars_packages SET price = ? WHERE id = ?", (prc, pid)); await db.commit()

async def update_stars_package_amount(pid, amt):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE stars_packages SET stars_amount = ? WHERE id = ?", (amt, pid)); await db.commit()

async def delete_stars_package(pid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("DELETE FROM stars_packages WHERE id = ?", (pid,)); await db.commit()

async def get_all_gifts():
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        return [dict(r) for r in await (await db.execute("SELECT * FROM custom_gifts ORDER BY stars_cost ASC, price ASC")).fetchall()]

async def get_gift(gid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        r = await (await db.execute("SELECT * FROM custom_gifts WHERE id = ?", (gid,))).fetchone()
        return dict(r) if r else None

async def add_custom_gift(name, stars_cost, price):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("INSERT INTO custom_gifts (name, stars_cost, price) VALUES (?, ?, ?)", (name, stars_cost, price)); await db.commit()

async def update_gift_price(gid, prc):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE custom_gifts SET price = ? WHERE id = ?", (prc, gid)); await db.commit()

async def update_gift_stars(gid, stars):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("UPDATE custom_gifts SET stars_cost = ? WHERE id = ?", (stars, gid)); await db.commit()

async def delete_gift(gid):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("DELETE FROM custom_gifts WHERE id = ?", (gid,)); await db.commit()

async def bulk_update_gift_prices(amount_diff: int):
    async with aiosqlite.connect(config.DB_NAME) as db:
        if amount_diff >= 0:
            await db.execute("UPDATE custom_gifts SET price = price + ?", (amount_diff,))
        else:
            await db.execute("UPDATE custom_gifts SET price = MAX(0, price + ?)", (amount_diff,))
        await db.commit()

async def get_all_required_channels():
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        return [dict(r) for r in await (await db.execute("SELECT * FROM required_channels")).fetchall()]

async def add_required_channel(ch_id: str, link: str, title: str = "کانال"):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("INSERT OR REPLACE INTO required_channels (channel_id, channel_link, channel_title) VALUES (?, ?, ?)", (ch_id.strip(), link.strip(), title.strip()))
        await db.commit()

async def delete_required_channel(ch_id: str):
    async with aiosqlite.connect(config.DB_NAME) as db:
        await db.execute("DELETE FROM required_channels WHERE channel_id = ? OR id = ?", (ch_id, ch_id))
        await db.commit()

async def get_users_paginated(page: int = 1, per_page: int = 10):
    offset = (page - 1) * per_page
    async with aiosqlite.connect(config.DB_NAME) as db:
        db.row_factory = aiosqlite.Row
        cursor = await db.execute("SELECT user_id, username, full_name, created_at, balance, kyc_status FROM users ORDER BY created_at DESC LIMIT ? OFFSET ?", (per_page, offset))
        rows = [dict(r) for r in await cursor.fetchall()]
        total = (await (await db.execute("SELECT COUNT(*) FROM users")).fetchone())[0]
        return rows, total

async def get_stats():
    async with aiosqlite.connect(config.DB_NAME) as db:
        t_users = (await (await db.execute("SELECT COUNT(*) FROM users")).fetchone())[0]
        v_users = (await (await db.execute("SELECT COUNT(*) FROM users WHERE is_verified = 1")).fetchone())[0]
        k_users = (await (await db.execute("SELECT COUNT(*) FROM users WHERE kyc_status = 2")).fetchone())[0]
        t_orders = (await (await db.execute("SELECT COUNT(*) FROM orders")).fetchone())[0]
        res_b = (await (await db.execute("SELECT SUM(balance) FROM users")).fetchone())[0]
        return {"total_users": t_users, "verified_users": v_users, "kyc_users": k_users, "total_orders": t_orders, "total_balance": res_b or 0}
EOF

cat << 'EOF' > handlers/admin.py
import asyncio, shutil, os, sys, zipfile
from aiogram import Router, types, F
from aiogram.filters import Command, BaseFilter
from aiogram.fsm.context import FSMContext
from aiogram.fsm.state import State, StatesGroup
from aiogram.types import InlineKeyboardMarkup, InlineKeyboardButton, FSInputFile
from config import config
import database as db

router = Router()

class AdminFilter(BaseFilter):
    async def __call__(self, event: types.TelegramObject) -> bool:
        u = getattr(event, "from_user", None)
        return await db.is_admin(u.id) if u else False

class AdminState(StatesGroup):
    waiting_for_broadcast = State()
    waiting_for_support_reply = State()
    waiting_for_card_info = State()
    waiting_for_welcome_text = State()
    waiting_for_kyc_text = State()
    waiting_for_guide_text = State()
    waiting_for_bonuses = State()
    waiting_for_deposit_limits = State()
    waiting_for_stars_prices = State()
    waiting_for_prem_prices = State()
    waiting_for_ton_price = State()
    waiting_for_self_price = State()
    waiting_for_btn_edit = State()
    waiting_for_emoji_normal = State()
    waiting_for_emoji_id = State()
    waiting_for_sticker_edit = State()
    waiting_for_coupon_create = State()
    waiting_for_channel = State()
    waiting_for_add_admin = State()
    waiting_for_remove_admin = State()
    waiting_for_gift_title = State()
    waiting_for_gift_stars = State()
    waiting_for_gift_price = State()
    waiting_for_gift_edit_price = State()
    waiting_for_gift_edit_stars = State()
    waiting_for_stars_base_50 = State()
    waiting_for_stars_min = State()
    waiting_for_stars_max = State()
    waiting_for_stars_title = State()
    waiting_for_stars_count = State()
    waiting_for_stars_price = State()
    waiting_for_stars_edit_price = State()
    waiting_for_stars_edit_amount = State()
    waiting_for_balance_user = State()
    waiting_for_balance_amount = State()
    waiting_for_bulk_gifts_price = State()
    waiting_for_revoke_kyc_user = State()

async def get_admin_keyboard():
    status = await db.get_setting("bot_status", "active")
    status_btn = "🟢 وضعیت: روشن (فعال)" if status == "active" else "🔴 وضعیت: خاموش (تعمیرات)"
    
    return InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="📊 آمار زنده ربات", style="primary", callback_data="admin_stats"), InlineKeyboardButton(text="📢 ارسال همگانی", style="primary", callback_data="admin_broadcast")],
        [InlineKeyboardButton(text="💰 تغییر موجودی کاربر", style="success", callback_data="admin_manage_balance"), InlineKeyboardButton(text="🪪 لغو / ابطال KYC کاربر", style="danger", callback_data="adm_revoke_kyc_start")],
        [InlineKeyboardButton(text="📝 ویرایش متن‌های ربات", style="primary", callback_data="adm_edit_texts"), InlineKeyboardButton(text="⭐️ مدیریت پیشرفته استارز", style="primary", callback_data="admin_stars_hub")],
        [InlineKeyboardButton(text="🎁 مدیریت گیفت‌ها", style="primary", callback_data="adm_gifts_list"), InlineKeyboardButton(text="🚀 مدیریت سلف‌بات‌ها", style="primary", callback_data="adm_self_hub")],
        [InlineKeyboardButton(text="✨ تنظیم ایموجی پرمیوم", style="primary", callback_data="adm_menu_emojis"), InlineKeyboardButton(text="🪙 قیمت واحد هر TON", style="primary", callback_data="admin_set_ton")],
        [InlineKeyboardButton(text="💎 قیمت پرمیوم", style="primary", callback_data="admin_set_prem"), InlineKeyboardButton(text="⭐️ قیمت بسته‌های استارز", style="primary", callback_data="admin_set_stars")],
        [InlineKeyboardButton(text="💳 تنظیم شماره کارت", style="primary", callback_data="admin_set_card"), InlineKeyboardButton(text="🎟 مدیریت کوپن‌ها", style="primary", callback_data="admin_coupons_menu")],
        [InlineKeyboardButton(text="👥 مدیریت ادمین‌ها", style="primary", callback_data="admin_manage_admins"), InlineKeyboardButton(text="✏️ ویرایش دکمه‌های منو", style="primary", callback_data="admin_menu_buttons")],
        [InlineKeyboardButton(text="🎭 تنظیم استیکرها", style="primary", callback_data="admin_menu_stickers"), InlineKeyboardButton(text="👥 لیست کاربران و تاریخ عضویت", style="primary", callback_data="adm_users_1")],
        [InlineKeyboardButton(text="🎁 تنظیم پاداش‌ها", style="primary", callback_data="admin_set_bonuses"), InlineKeyboardButton(text="💰 سقف و کف شارژ", style="primary", callback_data="admin_set_limits")],
        [InlineKeyboardButton(text="📢 مدیریت کانال‌های جوین اجباری", style="primary", callback_data="admin_set_channel")],
        [InlineKeyboardButton(text=status_btn, style="primary", callback_data="adm_toggle_bot_power"), InlineKeyboardButton(text="🔄 ری‌استارت ربات", style="danger", callback_data="adm_restart_bot")],
        [InlineKeyboardButton(text="📦 دریافت فایل‌های بکاپ کامل (.zip)", style="success", callback_data="admin_get_backup")]
    ])

@router.message(Command("admin"), AdminFilter())
async def admin_panel(message: types.Message, state: FSMContext = None):
    if state: await state.clear()
    await message.reply("👑 <b>پنل مدیریت جامع ربات</b>\nتمامی بخش‌ها از دکمه‌های زیر قابل کنترل هستند:", reply_markup=await get_admin_keyboard())

# --- ابطال و لغو KYC کاربر ---
@router.callback_query(F.data == "adm_revoke_kyc_start", AdminFilter())
async def revoke_kyc_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_revoke_kyc_user)
    text = (
        "🪪 <b>لغو و ابطال احراز هویت (KYC) کاربر:</b>\n\n"
        "لطفاً <b>آیدی عددی</b> یا <b>یوزرنیم کاربر</b> را ارسال فرمایید تا مدارک و احراز هویت او باطل شود:"
    )
    kb = InlineKeyboardMarkup(inline_keyboard=[[InlineKeyboardButton(text="❌ انصراف", style="danger", callback_data="admin_back")]])
    await callback.message.edit_text(text, reply_markup=kb)
    await callback.answer()

@router.message(AdminState.waiting_for_revoke_kyc_user, AdminFilter())
async def revoke_kyc_user_apply(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text):
        await state.clear()
        await message.reply("❌ عملیات لغو شد.", reply_markup=await get_admin_keyboard())
        return

    target_input = message.text.strip()
    user = await db.find_user(target_input)

    if not user:
        await message.reply(f"❌ کاربر «<code>{target_input}</code>» یافت نشد! مجدداً ارسال کنید:")
        return

    uid = user["user_id"]
    fname = user.get("full_name") or "کاربر"
    uname = f"@{user['username']}" if user.get("username") else "ندارد"

    # باطل کردن در دیتابیس
    await db.revoke_user_kyc(uid)

    admin_rep = (
        "✅ <b>احراز هویت کاربر با موفقیت باطل و لغو شد!</b>\n\n"
        f"👤 کاربر: <b>{fname}</b>\n"
        f"🆔 آیدی: <code>{uid}</code>\n"
        f"🏷 یوزرنیم: {uname}\n\n"
        "💡 <i>از این لحظه کاربر جهت خرید یا واریزی مجدداً باید احراز هویت انجام دهد.</i>"
    )
    await message.reply(admin_rep, reply_markup=await get_admin_keyboard())

    # ارسال اعلان به کاربر
    try:
        user_msg = (
            "⚠️ <b>وضعیت احراز هویت (KYC) شما توسط مدیریت لغو و باطل گردید.</b>\n"
            "جهت استفاده از خدمات و واریز وجه، لطفاً مجدداً از بخش کیف پول احراز هویت فرمایید."
        )
        await message.bot.send_message(chat_id=uid, text=user_msg, parse_mode="HTML")
    except Exception:
        pass

    await state.clear()

# --- روشن/خاموش کردن وضعیت ربات ---
@router.callback_query(F.data == "adm_toggle_bot_power", AdminFilter())
async def toggle_bot_power(callback: types.CallbackQuery):
    cur = await db.get_setting("bot_status", "active")
    new_st = "off" if cur == "active" else "active"
    await db.set_setting("bot_status", new_st)
    fa_st = "خاموش (حالت تعمیرات) 🔴" if new_st == "off" else "روشن و فعال 🟢"
    await callback.answer(f"وضعیت ربات به {fa_st} تغییر یافت.", show_alert=True)
    await callback.message.edit_reply_markup(reply_markup=await get_admin_keyboard())

# --- ری‌استارت هوشمند ربات از تلگرام ---
@router.callback_query(F.data == "adm_restart_bot", AdminFilter())
async def restart_bot_cmd(callback: types.CallbackQuery):
    await callback.message.edit_text("🔄 <b>در حال ری‌استارت ربات... لطفاً ۵ ثانیه صبر کنید.</b>")
    await callback.answer()
    os.execv(sys.executable, ['python3'] + sys.argv)

# --- منوی ویرایش متن‌های ربات ---
@router.callback_query(F.data == "adm_edit_texts", AdminFilter())
async def edit_texts_menu(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="💬 متن خوشامدگویی (/start)", style="primary", callback_data="adm_set_welcome_txt")],
        [InlineKeyboardButton(text="🪪 متن آموزش احراز هویت (KYC)", style="primary", callback_data="adm_set_kyc_txt")],
        [InlineKeyboardButton(text="📖 متن دکمه راهنما", style="primary", callback_data="adm_set_guide_txt")],
        [InlineKeyboardButton(text="🔙 بازگشت به پنل اصلی", style="primary", callback_data="admin_back")]
    ])
    await callback.message.edit_text("📝 <b>ویرایش متون مختلف ربات:</b>\nجهت تغییر هر متن روی دکمه آن کلیک کنید:", reply_markup=kb)
    await callback.answer()

@router.callback_query(F.data == "adm_set_welcome_txt", AdminFilter())
async def set_welcome_start(callback: types.CallbackQuery, state: FSMContext):
    w = await db.get_setting("welcome_text")
    await state.set_state(AdminState.waiting_for_welcome_text)
    await callback.message.answer(f"💬 <b>متن فعلی خوشامدگویی:</b>\n\n{w}\n\nمتن جدید را وارد کنید (نام کاربر: <code>{{name}}</code>):")
    await callback.answer()

@router.message(AdminState.waiting_for_welcome_text, AdminFilter())
async def set_welcome_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    await db.set_setting("welcome_text", message.text.strip())
    await state.clear(); await message.reply("✅ متن خوشامدگویی بروز شد.", reply_markup=await get_admin_keyboard())

@router.callback_query(F.data == "adm_set_kyc_txt", AdminFilter())
async def set_kyc_txt_start(callback: types.CallbackQuery, state: FSMContext):
    w = await db.get_setting("kyc_guide_text")
    await state.set_state(AdminState.waiting_for_kyc_text)
    await callback.message.answer(f"🪪 <b>متن فعلی احراز هویت:</b>\n\n{w}\n\nمتن جدید را وارد کنید:")
    await callback.answer()

@router.message(AdminState.waiting_for_kyc_text, AdminFilter())
async def set_kyc_txt_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    await db.set_setting("kyc_guide_text", message.text.strip())
    await state.clear(); await message.reply("✅ متن احراز هویت با موفقیت ذخیره شد.", reply_markup=await get_admin_keyboard())

@router.callback_query(F.data == "adm_set_guide_txt", AdminFilter())
async def set_guide_txt_start(callback: types.CallbackQuery, state: FSMContext):
    w = await db.get_setting("guide_text")
    await state.set_state(AdminState.waiting_for_guide_text)
    await callback.message.answer(f"📖 <b>متن فعلی راهنما:</b>\n\n{w}\n\nمتن جدید راهنما را وارد کنید:")
    await callback.answer()

@router.message(AdminState.waiting_for_guide_text, AdminFilter())
async def set_guide_txt_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    await db.set_setting("guide_text", message.text.strip())
    await state.clear(); await message.reply("✅ متن راهنما بروز شد.", reply_markup=await get_admin_keyboard())

# --- تغییر موجودی کاربر ---
@router.callback_query(F.data == "admin_manage_balance", AdminFilter())
async def admin_manage_balance_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_balance_user)
    text = "💰 <b>تغییر موجودی کاربر:</b>\n\nلطفاً <b>آیدی عددی</b> یا <b>یوزرنیم کاربر</b> را ارسال فرمایید:"
    kb = InlineKeyboardMarkup(inline_keyboard=[[InlineKeyboardButton(text="❌ انصراف", style="danger", callback_data="admin_back")]])
    await callback.message.edit_text(text, reply_markup=kb)
    await callback.answer()

@router.message(AdminState.waiting_for_balance_user, AdminFilter())
async def admin_balance_user_received(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text):
        await state.clear()
        await message.reply("❌ لغو شد.", reply_markup=await get_admin_keyboard())
        return

    target_input = message.text.strip()
    user = await db.find_user(target_input)

    if not user:
        await message.reply(f"❌ کاربری با شناسه «<code>{target_input}</code>» یافت نشد! مجدداً ارسال کنید:")
        return

    uid = user["user_id"]
    fname = user.get("full_name") or "بدون نام"
    uname = f"@{user['username']}" if user.get("username") else "ندارد"
    cur_bal = user.get("balance", 0)

    await state.update_data(target_uid=uid, target_fname=fname, cur_bal=cur_bal)
    await state.set_state(AdminState.waiting_for_balance_amount)

    info_text = (
        "👤 <b>اطلاعات کاربر:</b>\n\n"
        f"📝 نام: <b>{fname}</b>\n"
        f"🆔 آیدی: <code>{uid}</code>\n"
        f"🏷 یوزرنیم: {uname}\n"
        f"💳 موجودی فعلی: <b>{cur_bal:,} تومان</b>\n\n"
        "💵 مبلغ مورد نظر را وارد کنید (مثبت برای افزایش مثل <code>50000</code> و منفی برای کسر مثل <code>-20000</code>):"
    )
    await message.reply(info_text)

@router.message(AdminState.waiting_for_balance_amount, AdminFilter())
async def admin_balance_amount_apply(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text):
        await state.clear()
        await message.reply("❌ لغو شد.", reply_markup=await get_admin_keyboard())
        return

    raw_text = message.text.strip().replace(",", "")
    is_negative = raw_text.startswith("-")
    clean_num = db.clean_number(raw_text)

    if clean_num == 0:
        await message.reply("⚠️ عدد معتبر وارد کنید:")
        return

    amount_to_apply = -clean_num if is_negative else clean_num
    data = await state.get_data()
    uid = data["target_uid"]
    fname = data["target_fname"]

    await db.update_balance(uid, amount_to_apply)
    updated_user = await db.get_user(uid)
    new_bal = updated_user.get("balance", 0)

    act_text = f"افزایش یافت 🟢 (+{clean_num:,} ت)" if amount_to_apply > 0 else f"کسر گردید 🔴 (-{clean_num:,} ت)"
    admin_rep = f"✅ <b>موجودی کاربر {fname} ({uid}) با موفقیت {act_text}!</b>\n💰 موجودی جدید: <b>{new_bal:,} تومان</b>"
    await message.reply(admin_rep, reply_markup=await get_admin_keyboard())

    try:
        if amount_to_apply > 0:
            user_msg = f"🎉 <b>موجودی کیف پول شما توسط مدیریت به مبلغ {clean_num:,} تومان افزایش یافت!</b>\n💳 موجودی فعلی: <b>{new_bal:,} تومان</b>"
        else:
            user_msg = f"⚠️ <b>مبلغ {clean_num:,} تومان از حساب شما کسر گردید.</b>\n💳 موجودی فعلی: <b>{new_bal:,} تومان</b>"
        await message.bot.send_message(chat_id=uid, text=user_msg, parse_mode="HTML")
    except Exception: pass
    await state.clear()

# --- هاب مدیریت سلف‌بات‌ها ---
@router.callback_query(F.data == "adm_self_hub", AdminFilter())
async def admin_self_hub_menu(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    hourly_p = await db.get_int_setting("self_hourly_price", 600)
    per_min = max(1, int(hourly_p / 60))
    active_bots = await db.get_all_active_self_bots()

    text = (
        "🚀 <b>مدیریت سیستم سلف‌بات‌ها (PuLaSeLf):</b>\n\n"
        f"💰 <b>تعرفه فعلی:</b> هر ساعت <code>{hourly_p:,}</code> ت (دقیقه‌ای <b>{per_min:,} تومان</b>)\n"
        f"👥 <b>سلف‌های فعال در حال کار:</b> <b>{len(active_bots):,} نفر</b>"
    )
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="💰 تغییر تعرفه ساعتی سلف", style="primary", callback_data="adm_set_self_price")],
        [InlineKeyboardButton(text="👥 مشاهده سلف‌های فعال", style="primary", callback_data="adm_list_active_selfs")],
        [InlineKeyboardButton(text="🔙 بازگشت به پنل اصلی", style="primary", callback_data="admin_back")]
    ])
    await callback.message.edit_text(text, reply_markup=kb)
    await callback.answer()

@router.callback_query(F.data == "adm_set_self_price", AdminFilter())
async def set_self_price_start(callback: types.CallbackQuery, state: FSMContext):
    cur_p = await db.get_int_setting("self_hourly_price", 600)
    await state.set_state(AdminState.waiting_for_self_price)
    await callback.message.answer(f"💰 تعرفه فعلی: ساعتی <code>{cur_p:,}</code> ت\n\nمبلغ جدید برای هر ساعت مصرف سلف را وارد کنید:")
    await callback.answer()

@router.message(AdminState.waiting_for_self_price, AdminFilter())
async def set_self_price_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = db.clean_number(message.text)
    if p <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    await db.set_setting("self_hourly_price", str(p))
    await state.clear(); await message.reply(f"✅ تعرفه سلف به هر ساعت <b>{p:,} تومان</b> تغییر یافت.")

@router.callback_query(F.data == "adm_list_active_selfs", AdminFilter())
async def list_active_selfs(callback: types.CallbackQuery):
    active_bots = await db.get_all_active_self_bots()
    if not active_bots:
        await callback.message.answer("📭 هیچ سلف فعالی در حال حاضر وجود ندارد.")
        await callback.answer(); return
    text = "👥 <b>لیست سلف‌های آنلاین:</b>\n\n"
    for idx, b in enumerate(active_bots, 1):
        text += f"{idx}. 🆔 <code>{b['user_id']}</code> | 📱 <code>{b['phone_number']}</code>\n"
    await callback.message.answer(text); await callback.answer()

# --- هاب مدیریت استارز ---
@router.callback_query(F.data == "admin_stars_hub", AdminFilter())
async def stars_hub_menu(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    base_50 = await db.get_int_setting("stars_base_50_price", 75000)
    min_st = await db.get_int_setting("stars_min_buy", 50)
    max_st = await db.get_int_setting("stars_max_buy", 25000)

    text = f"⭐️ <b>تنظیمات استارز:</b>\n\n💰 قیمت ۵۰ استارز: <code>{base_50:,}</code> ت\n🔻 حداقل: <code>{min_st:,}</code> | 🔺 حداکثر: <code>{max_st:,}</code>"
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="💰 تنظیم قیمت ۵۰ استارز (پایه)", style="primary", callback_data="adm_set_stars_50_base")],
        [InlineKeyboardButton(text="🔻 تنظیم حداقل خرید", style="primary", callback_data="adm_set_stars_min"), InlineKeyboardButton(text="🔺 تنظیم حداکثر خرید", style="primary", callback_data="adm_set_stars_max")],
        [InlineKeyboardButton(text="📦 لیست و افزودن بسته‌های استارز", style="primary", callback_data="adm_stars_list")],
        [InlineKeyboardButton(text="🔙 بازگشت به پنل اصلی", style="primary", callback_data="admin_back")]
    ])
    await callback.message.edit_text(text, reply_markup=kb); await callback.answer()

@router.callback_query(F.data == "adm_set_stars_50_base", AdminFilter())
async def set_stars_50_start(callback: types.CallbackQuery, state: FSMContext):
    cur = await db.get_int_setting("stars_base_50_price", 75000)
    await state.set_state(AdminState.waiting_for_stars_base_50)
    await callback.message.answer(f"💰 قیمت ۵۰ استارز: <code>{cur:,}</code> ت\n\nقیمت جدید را وارد کنید:")
    await callback.answer()

@router.message(AdminState.waiting_for_stars_base_50, AdminFilter())
async def set_stars_50_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = db.clean_number(message.text)
    if p <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    await db.set_setting("stars_base_50_price", str(p))
    await state.clear(); await message.reply(f"✅ قیمت پایه ۵۰ استارز به <b>{p:,} ت</b> تغییر کرد.")

@router.callback_query(F.data == "adm_set_stars_min", AdminFilter())
async def set_stars_min_start(callback: types.CallbackQuery, state: FSMContext):
    cur = await db.get_int_setting("stars_min_buy", 50)
    await state.set_state(AdminState.waiting_for_stars_min)
    await callback.message.answer(f"🔻 حداقل فعلی: <code>{cur:,}</code> استارز\n\nمقدار جدید را وارد کنید:")
    await callback.answer()

@router.message(AdminState.waiting_for_stars_min, AdminFilter())
async def set_stars_min_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    amt = db.clean_number(message.text)
    if amt <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    await db.set_setting("stars_min_buy", str(amt))
    await state.clear(); await message.reply(f"✅ حداقل خرید به <b>{amt:,}</b> تنظیم شد.")

@router.callback_query(F.data == "adm_set_stars_max", AdminFilter())
async def set_stars_max_start(callback: types.CallbackQuery, state: FSMContext):
    cur = await db.get_int_setting("stars_max_buy", 25000)
    await state.set_state(AdminState.waiting_for_stars_max)
    await callback.message.answer(f"🔺 حداکثر فعلی: <code>{cur:,}</code> استارز\n\nمقدار جدید را وارد کنید:")
    await callback.answer()

@router.message(AdminState.waiting_for_stars_max, AdminFilter())
async def set_stars_max_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    amt = db.clean_number(message.text)
    if amt <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    await db.set_setting("stars_max_buy", str(amt))
    await state.clear(); await message.reply(f"✅ حداکثر خرید به <b>{amt:,}</b> تنظیم شد.")

# --- مدیریت بسته‌های استارز ---
@router.callback_query(F.data == "adm_stars_list", AdminFilter())
async def show_stars_list(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    packages = await db.get_all_stars_packages()
    buttons = [[InlineKeyboardButton(text=f"⭐️ {pkg['btn_title']} ── {pkg['price']:,} ت", style="primary", callback_data=f"adm_stview_{pkg['id']}")] for pkg in packages]
    buttons.append([InlineKeyboardButton(text="➕ افزودن بسته استارز جدید", style="success", callback_data="adm_add_stars_pkg")])
    buttons.append([InlineKeyboardButton(text="🔙 بازگشت به تنظیمات استارز", style="primary", callback_data="admin_stars_hub")])
    await callback.message.edit_text("⭐️ <b>لیست بسته‌های استارز:</b>", reply_markup=InlineKeyboardMarkup(inline_keyboard=buttons))
    await callback.answer()

@router.callback_query(F.data.startswith("adm_stview_"), AdminFilter())
async def view_stars_pkg(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    pkg_id = int(callback.data.split("_")[2])
    pkg = await db.get_stars_package(pkg_id)
    if not pkg: await callback.answer("❌ بسته یافت نشد!", show_alert=True); return
    text = f"⭐️ <b>بسته: {pkg['btn_title']}</b>\n\n⭐ تعداد: <b>{pkg['stars_amount']:,} استارز</b>\n💰 قیمت: <b>{pkg['price']:,} تومان</b>"
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="✏️ تغییر قیمت", style="primary", callback_data=f"adm_stchp_{pkg_id}"), InlineKeyboardButton(text="⭐ تغییر تعداد", style="primary", callback_data=f"adm_stcha_{pkg_id}")],
        [InlineKeyboardButton(text="🗑️ حذف بسته", style="danger", callback_data=f"adm_stdel_{pkg_id}")],
        [InlineKeyboardButton(text="🔙 بازگشت", style="primary", callback_data="adm_stars_list")]
    ])
    await callback.message.edit_text(text, reply_markup=kb); await callback.answer()

@router.callback_query(F.data.startswith("adm_stchp_"), AdminFilter())
async def change_stars_price_start(callback: types.CallbackQuery, state: FSMContext):
    await state.update_data(st_pkg_id=int(callback.data.split("_")[2]))
    await state.set_state(AdminState.waiting_for_stars_edit_price)
    await callback.message.answer("💰 قیمت جدید را وارد کنید:"); await callback.answer()

@router.message(AdminState.waiting_for_stars_edit_price, AdminFilter())
async def change_stars_price_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = db.clean_number(message.text)
    if p <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    data = await state.get_data()
    await db.update_stars_package_price(data["st_pkg_id"], p)
    await state.clear(); await message.reply(f"✅ قیمت بسته به <b>{p:,} تومان</b> تغییر کرد.")

@router.callback_query(F.data.startswith("adm_stcha_"), AdminFilter())
async def change_stars_amt_start(callback: types.CallbackQuery, state: FSMContext):
    await state.update_data(st_pkg_id=int(callback.data.split("_")[2]))
    await state.set_state(AdminState.waiting_for_stars_edit_amount)
    await callback.message.answer("⭐ تعداد استارز جدید را وارد کنید:"); await callback.answer()

@router.message(AdminState.waiting_for_stars_edit_amount, AdminFilter())
async def change_stars_amt_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    amt = db.clean_number(message.text)
    if amt <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    data = await state.get_data()
    await db.update_stars_package_amount(data["st_pkg_id"], amt)
    await state.clear(); await message.reply(f"✅ تعداد استارز به <b>{amt:,}</b> تغییر یافت.")

@router.callback_query(F.data.startswith("adm_stdel_"), AdminFilter())
async def delete_stars_pkg(callback: types.CallbackQuery, state: FSMContext = None):
    await db.delete_stars_package(int(callback.data.split("_")[2]))
    await callback.answer("🗑 حذف شد.", show_alert=True); await show_stars_list(callback, state)

@router.callback_query(F.data == "adm_add_stars_pkg", AdminFilter())
async def add_stars_pkg_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_stars_title)
    await callback.message.answer("📝 متن روی دکمه شیشه‌ای چی باشه؟ (مثال: <code>⭐️ ۱۰۰ استارز ویژه</code>)"); await callback.answer()

@router.message(AdminState.waiting_for_stars_title, AdminFilter())
async def add_stars_pkg_title(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    await state.update_data(st_title=message.text.strip())
    await state.set_state(AdminState.waiting_for_stars_count)
    await message.answer("⭐ این بسته چند استارز هست؟ (مثال: <code>100</code>)")

@router.message(AdminState.waiting_for_stars_count, AdminFilter())
async def add_stars_pkg_count(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    amt = db.clean_number(message.text)
    if amt <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    await state.update_data(st_amount=amt)
    await state.set_state(AdminState.waiting_for_stars_price)
    await message.answer("💰 قیمت به تومان چند باشه؟ (مثال: <code>145000</code>)")

@router.message(AdminState.waiting_for_stars_price, AdminFilter())
async def add_stars_pkg_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = db.clean_number(message.text)
    if p <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    data = await state.get_data()
    await db.add_stars_package(data["st_title"], data["st_amount"], p)
    await state.clear(); await message.reply(f"✅ بسته <b>{data['st_title']}</b> اضافه گردید.")

# --- مدیریت گیفت‌ها ---
@router.callback_query(F.data == "adm_gifts_list", AdminFilter())
async def show_gifts_list(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    gifts = await db.get_all_gifts()
    buttons = []
    for g in gifts:
        st_txt = f" ({g['stars_cost']} ⭐)" if g.get('stars_cost') else ""
        buttons.append([InlineKeyboardButton(text=f"🎁 {g['name']}{st_txt} ── {g['price']:,} ت", style="primary", callback_data=f"adm_gview_{g['id']}")])
    
    buttons.append([InlineKeyboardButton(text="⚡️ تغییر دسته‌جمعی قیمت همه گیفت‌ها", style="success", callback_data="adm_bulk_gifts_price")])
    buttons.append([InlineKeyboardButton(text="➕ افزودن گیفت جدید", style="primary", callback_data="adm_add_gift")])
    buttons.append([InlineKeyboardButton(text="🔙 بازگشت به پنل", style="primary", callback_data="admin_back")])
    await callback.message.edit_text("🎁 <b>مدیریت گیفت‌های تلگرام:</b>", reply_markup=InlineKeyboardMarkup(inline_keyboard=buttons))
    await callback.answer()

@router.callback_query(F.data == "adm_bulk_gifts_price", AdminFilter())
async def bulk_gifts_price_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_bulk_gifts_price)
    text = "⚡️ مبلغ مورد نظر برای افزایش یا کاهش قیمت <b>تمام گیفت‌ها</b> را به تومان وارد کنید (مثال: <code>5000</code> یا <code>-5000</code>):"
    kb = InlineKeyboardMarkup(inline_keyboard=[[InlineKeyboardButton(text="❌ انصراف", style="danger", callback_data="adm_gifts_list")]])
    await callback.message.edit_text(text, reply_markup=kb); await callback.answer()

@router.message(AdminState.waiting_for_bulk_gifts_price, AdminFilter())
async def bulk_gifts_price_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text):
        await state.clear(); await message.reply("❌ لغو شد.", reply_markup=await get_admin_keyboard()); return
    raw_text = message.text.strip().replace(",", "")
    is_neg = raw_text.startswith("-")
    num = db.clean_number(raw_text)
    if num == 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    diff = -num if is_neg else num
    await db.bulk_update_gift_prices(diff)
    gifts = await db.get_all_gifts()
    act = f"افزایش یافت (+{num:,} ت)" if diff > 0 else f"کاهش یافت (-{num:,} ت)"
    rep = f"✅ <b>قیمت گیفت‌ها {act}!</b>\n\n"
    for idx, g in enumerate(gifts, 1):
        rep += f"{idx}. 🎁 {g['name']} ➔ <b>{g['price']:,} تومان</b>\n"
    await state.clear(); await message.reply(rep, reply_markup=await get_admin_keyboard())

@router.callback_query(F.data.startswith("adm_gview_"), AdminFilter())
async def view_gift_details(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    gid = int(callback.data.split("_")[2])
    g = await db.get_gift(gid)
    if not g: await callback.answer("❌ گیفت یافت نشد!", show_alert=True); return
    text = f"🎁 <b>گیفت: {g['name']}</b>\n\n⭐ استارز: <b>{g['stars_cost']} ⭐</b>\n💰 قیمت: <b>{g['price']:,} تومان</b>"
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="✏️ تغییر قیمت", style="primary", callback_data=f"adm_gchp_{gid}"), InlineKeyboardButton(text="⭐ تغییر استارز", style="primary", callback_data=f"adm_gchs_{gid}")],
        [InlineKeyboardButton(text="🗑️ حذف این گیفت", style="danger", callback_data=f"adm_gdel_{gid}")],
        [InlineKeyboardButton(text="🔙 بازگشت به لیست", style="primary", callback_data="adm_gifts_list")]
    ])
    await callback.message.edit_text(text, reply_markup=kb); await callback.answer()

@router.callback_query(F.data.startswith("adm_gchp_"), AdminFilter())
async def change_gift_price_start(callback: types.CallbackQuery, state: FSMContext):
    await state.update_data(edit_gid=int(callback.data.split("_")[2]))
    await state.set_state(AdminState.waiting_for_gift_edit_price)
    await callback.message.answer("💰 قیمت جدید گیفت را به تومان وارد کنید:"); await callback.answer()

@router.message(AdminState.waiting_for_gift_edit_price, AdminFilter())
async def change_gift_price_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = db.clean_number(message.text)
    if p <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    data = await state.get_data()
    await db.update_gift_price(data["edit_gid"], p)
    await state.clear(); await message.reply(f"✅ قیمت گیفت به <b>{p:,} تومان</b> بروز شد.")

@router.callback_query(F.data.startswith("adm_gchs_"), AdminFilter())
async def change_gift_stars_start(callback: types.CallbackQuery, state: FSMContext):
    await state.update_data(edit_gid=int(callback.data.split("_")[2]))
    await state.set_state(AdminState.waiting_for_gift_edit_stars)
    await callback.message.answer("⭐ این گیفت چند استارزی است؟:"); await callback.answer()

@router.message(AdminState.waiting_for_gift_edit_stars, AdminFilter())
async def change_gift_stars_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    stars = db.clean_number(message.text)
    data = await state.get_data()
    await db.update_gift_stars(data["edit_gid"], stars)
    await state.clear(); await message.reply(f"✅ استارز گیفت به <b>{stars} ⭐</b> تغییر کرد.")

@router.callback_query(F.data.startswith("adm_gdel_"), AdminFilter())
async def delete_gift_handler(callback: types.CallbackQuery, state: FSMContext = None):
    await db.delete_gift(int(callback.data.split("_")[2]))
    await callback.answer("🗑 حذف شد.", show_alert=True); await show_gifts_list(callback, state)

@router.callback_query(F.data == "adm_add_gift", AdminFilter())
async def add_gift_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_gift_title)
    await callback.message.answer("📝 نام گیفت چی باشه؟ (مثال: <code>🧸 خرس تدی</code>)"); await callback.answer()

@router.message(AdminState.waiting_for_gift_title, AdminFilter())
async def add_gift_title_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    await state.update_data(g_title=message.text.strip())
    await state.set_state(AdminState.waiting_for_gift_stars)
    await message.answer("⭐ این گیفت چند استارزیه؟ (مثال: <code>50</code>)")

@router.message(AdminState.waiting_for_gift_stars, AdminFilter())
async def add_gift_stars_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    stars = db.clean_number(message.text)
    await state.update_data(g_stars=stars)
    await state.set_state(AdminState.waiting_for_gift_price)
    await message.answer("💰 قیمت این گیفت به تومان چند باشه؟")

@router.message(AdminState.waiting_for_gift_price, AdminFilter())
async def add_gift_price_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = db.clean_number(message.text)
    if p <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    data = await state.get_data()
    await db.add_custom_gift(data["g_title"], data["g_stars"], p)
    await state.clear(); await message.reply(f"✅ گیفت <b>{data['g_title']}</b> اضافه شد!")

# --- آمار زنده ---
@router.callback_query(F.data == "admin_stats", AdminFilter())
async def show_stats(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    stats = await db.get_stats()
    text = f"📊 <b>آمار وضعیت ربات:</b>\n\n👥 کل کاربران: <b>{stats['total_users']:,} نفر</b>\n🪪 احراز هویت شده: <b>{stats['kyc_users']:,} نفر</b>\n🛍 کل سفارشات: <b>{stats['total_orders']:,} عدد</b>\n💰 مجموع کیف‌پول‌ها: <b>{stats['total_balance']:,} تومان</b>"
    await callback.message.edit_text(text, reply_markup=await get_admin_keyboard()); await callback.answer()

# --- ارسال همگانی ---
@router.callback_query(F.data == "admin_broadcast", AdminFilter())
async def start_broadcast(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_broadcast)
    await callback.message.answer("📢 پیام همگانی را بفرستید:"); await callback.answer()

@router.message(AdminState.waiting_for_broadcast, AdminFilter())
async def send_broadcast(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    users = await db.get_all_user_ids()
    await message.reply(f"🚀 در حال ارسال به {len(users)} کاربر...")
    success, failed = 0, 0
    for uid in users:
        try: await message.copy_to(chat_id=uid); success += 1; await asyncio.sleep(0.04)
        except Exception: failed += 1
    await message.reply(f"✅ ارسال پایان یافت.\n✔️ موفق: {success}\n❌ ناموفق: {failed}"); await state.clear()

# --- ایموجی پرمیوم خودکار ---
@router.callback_query(F.data == "adm_menu_emojis", AdminFilter())
async def list_emojis_menu(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    emojis = await db.get_emoji_map()
    buttons = [[InlineKeyboardButton(text=f"🗑️ حذف {norm} ➔ {cid}", style="danger", callback_data=f"adm_emodel_{norm}")] for norm, cid in emojis.items()]
    buttons.append([InlineKeyboardButton(text="➕ افزودن / جایگزینی ایموجی", style="success", callback_data="adm_emo_add")])
    buttons.append([InlineKeyboardButton(text="🔙 بازگشت به پنل", style="primary", callback_data="admin_back")])
    await callback.message.edit_text("✨ <b>لیست ایموجی‌های فعال:</b>", reply_markup=InlineKeyboardMarkup(inline_keyboard=buttons)); await callback.answer()

@router.callback_query(F.data == "adm_emo_add", AdminFilter())
async def add_emoji_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_emoji_normal)
    await callback.message.answer("💎 ایموجی معمولی را بفرستید:"); await callback.answer()

@router.message(AdminState.waiting_for_emoji_normal, AdminFilter())
async def add_emoji_norm_rec(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    await state.update_data(norm_emo=message.text.strip())
    await state.set_state(AdminState.waiting_for_emoji_id)
    await message.answer("🔢 حالا شناسه عددی (Custom Emoji ID) را ارسال کنید:")

@router.message(AdminState.waiting_for_emoji_id, AdminFilter())
async def add_emoji_id_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    cid = message.text.strip()
    data = await state.get_data()
    await db.set_emoji_map(data["norm_emo"], cid)
    await state.clear(); await message.reply(f"✅ موفق! زین پس {data['norm_emo']} خودکار پرمیوم می‌شود.")

@router.callback_query(F.data.startswith("adm_emodel_"), AdminFilter())
async def delete_emoji_handler(callback: types.CallbackQuery, state: FSMContext = None):
    await db.delete_emoji_map(callback.data.replace("adm_emodel_", ""))
    await callback.answer("🗑 حذف شد.", show_alert=True); await list_emojis_menu(callback, state)

# --- قیمت TON ---
@router.callback_query(F.data == "admin_set_ton", AdminFilter())
async def set_ton_start(callback: types.CallbackQuery, state: FSMContext):
    cur_p = await db.get_int_setting("ton_unit_price", 330423)
    await callback.message.answer(f"🪙 قیمت هر TON: <code>{cur_p:,}</code> ت\n\nقیمت جدید را وارد کنید:")
    await state.set_state(AdminState.waiting_for_ton_price); await callback.answer()

@router.message(AdminState.waiting_for_ton_price, AdminFilter())
async def set_ton_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = db.clean_number(message.text)
    if p <= 0: await message.reply("⚠️ عدد معتبر وارد کنید:"); return
    await db.set_setting("ton_unit_price", str(p)); await state.clear(); await message.reply(f"✅ قیمت هر TON به <b>{p:,} ت</b> تغییر کرد.")

# --- قیمت پرمیوم ---
@router.callback_query(F.data == "admin_set_prem", AdminFilter())
async def set_prem_start(callback: types.CallbackQuery, state: FSMContext):
    p3 = await db.get_int_setting("prem_3m", 360000); p6 = await db.get_int_setting("prem_6m", 680000); p12 = await db.get_int_setting("prem_12m", 1250000)
    await callback.message.answer(f"💎 <b>قیمت‌های پرمیوم:</b>\n• ۳ ماهه: {p3:,}\n• ۶ ماهه: {p6:,}\n• ۱۲ ماهه: {p12:,}\n\nالگو: <code>370000-700000-1300000</code>")
    await state.set_state(AdminState.waiting_for_prem_prices); await callback.answer()

@router.message(AdminState.waiting_for_prem_prices, AdminFilter())
async def set_prem_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    parts = [db.clean_number(p) for p in message.text.split("-") if db.clean_number(p) > 0]
    if len(parts) != 3: await message.reply("⚠️ ۳ قیمت با خط فاصله بفرستید."); return
    await db.set_setting("prem_3m", str(parts[0])); await db.set_setting("prem_6m", str(parts[1])); await db.set_setting("prem_12m", str(parts[2]))
    await state.clear(); await message.reply("✅ قیمت‌های پرمیوم ذخیره شدند.")

# --- قیمت‌های استارز ---
@router.callback_query(F.data == "admin_set_stars", AdminFilter())
async def set_stars_start(callback: types.CallbackQuery, state: FSMContext):
    p50 = await db.get_int_setting("stars_50", 75000)
    p100 = await db.get_int_setting("stars_100", 145000)
    p250 = await db.get_int_setting("stars_250", 355000)
    p500 = await db.get_int_setting("stars_500", 695000)
    p1000 = await db.get_int_setting("stars_1000", 1380000)
    await callback.message.answer(f"⭐️ <b>قیمت‌های استارز:</b>\n۵۰: {p50:,} | ۱۰۰: {p100:,} | ۲۵۰: {p250:,} | ۵۰۰: {p500:,} | ۱۰۰۰: {p1000:,}\n\nالگو: <code>80000-150000-360000-700000-1400000</code>")
    await state.set_state(AdminState.waiting_for_stars_prices); await callback.answer()

@router.message(AdminState.waiting_for_stars_prices, AdminFilter())
async def set_stars_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    parts = [db.clean_number(p) for p in message.text.split("-") if db.clean_number(p) > 0]
    if len(parts) != 5: await message.reply("⚠️ ۵ قیمت با خط فاصله بفرستید."); return
    await db.set_setting("stars_50", str(parts[0])); await db.set_setting("stars_100", str(parts[1]))
    await db.set_setting("stars_250", str(parts[2])); await db.set_setting("stars_500", str(parts[3]))
    await db.set_setting("stars_1000", str(parts[4]))
    await state.clear(); await message.reply("✅ قیمت‌های استارز ذخیره شدند.")

# --- شماره کارت ---
@router.callback_query(F.data == "admin_set_card", AdminFilter())
async def set_card_start(callback: types.CallbackQuery, state: FSMContext):
    c = await db.get_setting("card_number"); h = await db.get_setting("card_holder")
    await callback.message.answer(f"💳 <b>کارت فعلی:</b>\n<code>{c}</code>\n<b>{h}</b>\n\nالگو: <code>شماره‌کارت-نام</code>")
    await state.set_state(AdminState.waiting_for_card_info); await callback.answer()

@router.message(AdminState.waiting_for_card_info, AdminFilter())
async def set_card_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    if "-" not in message.text: await message.reply("⚠️ الگو: <code>شماره‌کارت-نام</code>"); return
    num, holder = message.text.split("-", 1)
    await db.set_setting("card_number", str(db.clean_number(num))); await db.set_setting("card_holder", holder.strip())
    await state.clear(); await message.reply("✅ مشخصات کارت ذخیره شد.")

# --- کوپن‌ها ---
@router.callback_query(F.data == "admin_coupons_menu", AdminFilter())
async def coupons_menu(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="➕ ساخت کوپن", style="primary", callback_data="admin_add_coupon")],
        [InlineKeyboardButton(text="📋 لیست و حذف کوپن‌ها", style="primary", callback_data="admin_list_coupons")],
        [InlineKeyboardButton(text="🔙 بازگشت به پنل", style="primary", callback_data="admin_back")]
    ])
    await callback.message.edit_text("🎟 <b>مدیریت کوپن‌های تخفیف:</b>", reply_markup=kb); await callback.answer()

@router.callback_query(F.data == "admin_add_coupon", AdminFilter())
async def add_coupon_start(callback: types.CallbackQuery, state: FSMContext):
    await callback.message.answer("الگو: <code>کد-درصد-مبلغ‌ثابت-تعداد</code>\nمثال: <code>YALDA-20-0-100</code>")
    await state.set_state(AdminState.waiting_for_coupon_create); await callback.answer()

@router.message(AdminState.waiting_for_coupon_create, AdminFilter())
async def add_coupon_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = message.text.split("-")
    if len(p) != 4: await message.reply("⚠️ ۴ بخش با خط فاصله بفرستید."); return
    await db.create_coupon(p[0].strip(), db.clean_number(p[1]), db.clean_number(p[2]), max(1, db.clean_number(p[3])))
    await state.clear(); await message.reply(f"✅ کوپن <code>{p[0].upper()}</code> ایجاد شد.")

@router.callback_query(F.data == "admin_list_coupons", AdminFilter())
async def list_coupons(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    coupons = await db.get_all_coupons()
    buttons = [[InlineKeyboardButton(text=f"🗑 حذف {c['code']}", style="danger", callback_data=f"del_cp_{c['code']}")] for c in coupons]
    buttons.append([InlineKeyboardButton(text="🔙 بازگشت", style="primary", callback_data="admin_coupons_menu")])
    await callback.message.edit_text("📋 <b>لیست کوپن‌ها:</b>", reply_markup=InlineKeyboardMarkup(inline_keyboard=buttons)); await callback.answer()

@router.callback_query(F.data.startswith("del_cp_"), AdminFilter())
async def delete_coupon_handler(callback: types.CallbackQuery, state: FSMContext = None):
    await db.delete_coupon(callback.data.split("_")[2])
    await callback.answer("🗑 حذف شد.", show_alert=True); await list_coupons(callback, state)

# --- مدیریت ادمین‌ها ---
@router.callback_query(F.data == "admin_manage_admins", AdminFilter())
async def manage_admins_menu(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    admins = await db.get_all_admins()
    text = "👥 <b>لیست ادمین‌ها:</b>\n\n" + "\n".join([f"• <code>{a}</code>" for a in admins])
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="➕ افزودن ادمین", style="primary", callback_data="admin_add_new"), InlineKeyboardButton(text="🗑 حذف ادمین", style="danger", callback_data="admin_del_exist")],
        [InlineKeyboardButton(text="🔙 بازگشت", style="primary", callback_data="admin_back")]
    ])
    await callback.message.edit_text(text, reply_markup=kb); await callback.answer()

@router.callback_query(F.data == "admin_add_new", AdminFilter())
async def add_admin_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_add_admin)
    await callback.message.answer("آیدی عددی ادمین جدید را بفرستید:"); await callback.answer()

@router.message(AdminState.waiting_for_add_admin, AdminFilter())
async def add_admin_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    uid = db.clean_number(message.text)
    if not uid: await message.reply("⚠️ آیدی نامعتبر است."); return
    await db.add_admin(uid); await state.clear(); await message.reply(f"✅ ادمین <code>{uid}</code> اضافه شد.")

@router.callback_query(F.data == "admin_del_exist", AdminFilter())
async def del_admin_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_remove_admin)
    await callback.message.answer("آیدی عددی ادمین برای حذف را بفرستید:"); await callback.answer()

@router.message(AdminState.waiting_for_remove_admin, AdminFilter())
async def del_admin_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    uid = db.clean_number(message.text)
    if uid == config.ADMIN_ID: await message.reply("❌ مالک قابل حذف نیست!"); return
    await db.remove_admin(uid); await state.clear(); await message.reply(f"✅ دسترسی <code>{uid}</code> حذف شد.")

# --- دکمه‌های منو ---
@router.callback_query(F.data == "admin_menu_buttons", AdminFilter())
async def list_buttons_edit(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="ویرایش استارز", style="primary", callback_data="edit_btn_stars"), InlineKeyboardButton(text="ویرایش پرمیوم", style="primary", callback_data="edit_btn_premium")],
        [InlineKeyboardButton(text="ویرایش تون", style="primary", callback_data="edit_btn_ton"), InlineKeyboardButton(text="ویرایش گیفت‌ها", style="primary", callback_data="edit_btn_gifts")],
        [InlineKeyboardButton(text="ویرایش کیف پول", style="primary", callback_data="edit_btn_wallet"), InlineKeyboardButton(text="ویرایش زیرمجموعه‌گیری", style="primary", callback_data="edit_btn_ref")],
        [InlineKeyboardButton(text="ویرایش راهنما", style="primary", callback_data="edit_btn_guide"), InlineKeyboardButton(text="ویرایش پشتیبانی", style="primary", callback_data="edit_btn_support")],
        [InlineKeyboardButton(text="🔙 بازگشت به پنل", style="primary", callback_data="admin_back")]
    ])
    await callback.message.edit_text("✏️ <b>دکمه مورد نظر برای تغییر را انتخاب کنید:</b>", reply_markup=kb); await callback.answer()

@router.callback_query(F.data.startswith("edit_btn_"), AdminFilter())
async def start_btn_edit(callback: types.CallbackQuery, state: FSMContext):
    await state.update_data(btn_key=callback.data.replace("edit_", ""))
    await state.set_state(AdminState.waiting_for_btn_edit)
    await callback.message.answer("عنوان جدید دکمه را بفرستید:"); await callback.answer()

@router.message(AdminState.waiting_for_btn_edit, AdminFilter())
async def save_btn_edit(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    data = await state.get_data()
    await db.set_setting(data["btn_key"], message.text.strip())
    await state.clear(); await message.reply("✅ عنوان دکمه ذخیره شد.")

# --- استیکرها ---
@router.callback_query(F.data == "admin_menu_stickers", AdminFilter())
async def list_stickers_edit(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    kb = InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="استیکر خوشامدگویی", style="primary", callback_data="setstk_sticker_welcome"), InlineKeyboardButton(text="استیکر موفقیت", style="primary", callback_data="setstk_sticker_success")],
        [InlineKeyboardButton(text="استیکر احراز هویت", style="primary", callback_data="setstk_sticker_kyc"), InlineKeyboardButton(text="استیکر کیف پول", style="primary", callback_data="setstk_sticker_wallet")],
        [InlineKeyboardButton(text="🔙 بازگشت به پنل", style="primary", callback_data="admin_back")]
    ])
    await callback.message.edit_text("🎭 <b>تنظیم استیکرها:</b>", reply_markup=kb); await callback.answer()

@router.callback_query(F.data.startswith("setstk_"), AdminFilter())
async def start_sticker_edit(callback: types.CallbackQuery, state: FSMContext):
    await state.update_data(stk_key=callback.data.replace("setstk_", ""))
    await state.set_state(AdminState.waiting_for_sticker_edit)
    await callback.message.answer("لطفاً یک استیکر بفرستید:"); await callback.answer()

@router.message(AdminState.waiting_for_sticker_edit, AdminFilter())
async def save_sticker_edit(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    fid = message.sticker.file_id if message.sticker else message.text.strip()
    data = await state.get_data()
    await db.set_setting(data["stk_key"], fid); await state.clear(); await message.reply("✅ استیکر ذخیره شد.")

# --- لیست کاربران ---
@router.callback_query(F.data.startswith("adm_users_"), AdminFilter())
async def show_users_list(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    page = int(callback.data.split("_")[2])
    per_page = 8
    users, total_users = await db.get_users_paginated(page=page, per_page=per_page)
    total_pages = max(1, (total_users + per_page - 1) // per_page)
    text = f"👥 <b>لیست کاربران ربات (صفحه {page} از {total_pages})</b>\n📊 کل کاربران: <b>{total_users:,} نفر</b>\n\n"
    
    for idx, u in enumerate(users, start=(page - 1) * per_page + 1):
        uname = f"@{u['username']}" if u['username'] else "ندارد"
        join_date = str(u['created_at'])[:16]
        text += f"{idx}. {u['full_name']} ({uname})\n   🆔 <code>{u['user_id']}</code> | 💰 {u['balance']:,} ت\n   📅 تاریخ: <code>{join_date}</code>\n\n"

    nav_buttons = []
    if page > 1: nav_buttons.append(InlineKeyboardButton(text="⬅️ قبلی", style="primary", callback_data=f"adm_users_{page - 1}"))
    if page < total_pages: nav_buttons.append(InlineKeyboardButton(text="بعدی ➡️", style="primary", callback_data=f"adm_users_{page + 1}"))
    kb_rows = []
    if nav_buttons: kb_rows.append(nav_buttons)
    kb_rows.append([InlineKeyboardButton(text="🔙 بازگشت به پنل", style="primary", callback_data="admin_back")])
    await callback.message.edit_text(text, reply_markup=InlineKeyboardMarkup(inline_keyboard=kb_rows)); await callback.answer()

# --- پاداش‌ها ---
@router.callback_query(F.data == "admin_set_bonuses", AdminFilter())
async def set_bonuses_start(callback: types.CallbackQuery, state: FSMContext):
    wb = await db.get_int_setting("welcome_bonus", 10000); rb = await db.get_int_setting("referral_bonus", 5000)
    await callback.message.answer(f"🎁 <b>پاداش فعلی:</b>\nورود: {wb:,} ت | دعوت: {rb:,} ت\n\nالگو: <code>ورود-دعوت</code>")
    await state.set_state(AdminState.waiting_for_bonuses); await callback.answer()

@router.message(AdminState.waiting_for_bonuses, AdminFilter())
async def set_bonuses_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = [db.clean_number(x) for x in message.text.split("-") if db.clean_number(x) >= 0]
    if len(p) != 2: await message.reply("⚠️ ۲ مبلغ با خط فاصله بفرستید."); return
    await db.set_setting("welcome_bonus", str(p[0])); await db.set_setting("referral_bonus", str(p[1]))
    await state.clear(); await message.reply("✅ پاداش‌ها ذخیره شدند.")

# --- سقف و کف شارژ ---
@router.callback_query(F.data == "admin_set_limits", AdminFilter())
async def set_limits_start(callback: types.CallbackQuery, state: FSMContext):
    mi = await db.get_int_setting("min_deposit", 100000); ma = await db.get_int_setting("max_deposit", 5000000)
    await callback.message.answer(f"💰 <b>کف:</b> {mi:,} ت | <b>سقف:</b> {ma:,} ت\n\nالگو: <code>کف-سقف</code>")
    await state.set_state(AdminState.waiting_for_deposit_limits); await callback.answer()

@router.message(AdminState.waiting_for_deposit_limits, AdminFilter())
async def set_limits_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    p = [db.clean_number(x) for x in message.text.split("-") if db.clean_number(x) > 0]
    if len(p) != 2: await message.reply("⚠️ ۲ عدد معتبر بفرستید."); return
    await db.set_setting("min_deposit", str(p[0])); await db.set_setting("max_deposit", str(p[1]))
    await state.clear(); await message.reply("✅ سقف و کف ذخیره شدند.")

# --- مدیریت کانال‌های جوین اجباری ---
@router.callback_query(F.data == "admin_set_channel", AdminFilter())
async def manage_channels_menu(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    channels = await db.get_all_required_channels()
    buttons = []
    text = "📢 <b>مدیریت کانال‌های جوین اجباری:</b>\n\n"
    if channels:
        for idx, ch in enumerate(channels, 1):
            text += f"{idx}. <b>{ch['channel_title']}</b> (<code>{ch['channel_id']}</code>)\n🔗 {ch['channel_link']}\n\n"
            buttons.append([InlineKeyboardButton(text=f"🗑 حذف {ch['channel_title']}", style="danger", callback_data=f"del_reqch_{ch['id']}")])
    else:
        text += "<i>هیچ کانال جوین اجباری فعالی وجود ندارد.</i>\n"

    buttons.append([InlineKeyboardButton(text="➕ افزودن کانال جدید", style="success", callback_data="add_reqch_start")])
    buttons.append([InlineKeyboardButton(text="🔙 بازگشت به پنل", style="primary", callback_data="admin_back")])
    await callback.message.edit_text(text, reply_markup=InlineKeyboardMarkup(inline_keyboard=buttons))
    await callback.answer()

@router.callback_query(F.data == "add_reqch_start", AdminFilter())
async def add_channel_start(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(AdminState.waiting_for_channel)
    text = "📢 الگو: <code>آیدی‌کانال-لینک‌کانال-عنوان‌کانال</code>\nمثال:\n<code>@MyChannel-https://t.me/MyChannel-کانال اطلاع‌رسانی</code>"
    await callback.message.answer(text); await callback.answer()

@router.message(AdminState.waiting_for_channel, AdminFilter())
async def add_channel_save(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text): await state.clear(); await message.reply("❌ لغو شد."); return
    parts = message.text.split("-")
    if len(parts) < 2: await message.reply("⚠️ الگو: <code>آیدی-لینک-عنوان</code>"); return
    ch_id = parts[0].strip()
    link = parts[1].strip()
    title = parts[2].strip() if len(parts) > 2 else "کانال"
    await db.add_required_channel(ch_id, link, title)
    await state.clear(); await message.reply(f"✅ کانال <b>{title}</b> به لیست جوین اجباری اضافه شد.")

@router.callback_query(F.data.startswith("del_reqch_"), AdminFilter())
async def delete_channel_handler(callback: types.CallbackQuery, state: FSMContext = None):
    ch_pk = callback.data.replace("del_reqch_", "")
    await db.delete_required_channel(ch_pk)
    await callback.answer("🗑 کانال حذف شد.", show_alert=True)
    await manage_channels_menu(callback, state)

# --- دریافت بکاپ پروژه ---
@router.message(Command("backup"), AdminFilter())
@router.callback_query(F.data == "admin_get_backup", AdminFilter())
async def send_project_backup(event: types.Message | types.CallbackQuery):
    user_id = event.from_user.id
    msg = event.message if isinstance(event, types.CallbackQuery) else event
    wait_msg = await msg.answer("⏳ در حال ساخت فایل زیپ بکاپ...")
    zip_name = "project_backup.zip"
    if os.path.exists(zip_name): os.remove(zip_name)
    with zipfile.ZipFile(zip_name, "w", zipfile.ZIP_DEFLATED) as zf:
        for root, dirs, files in os.walk("."):
            dirs[:] = [d for d in dirs if d not in [".venv", "venv", ".git", "__pycache__", "env"]]
            for file in files:
                if file.endswith((".py", ".db", ".env", ".txt", ".json", ".md")):
                    fp = os.path.join(root, file)
                    zf.write(fp, arcname=os.path.relpath(fp, "."))
    await event.bot.send_document(chat_id=user_id, document=FSInputFile(zip_name), caption="📦 <b>بکاپ کامل سورس و دیتابیس ربات.</b>", request_timeout=120)
    await wait_msg.delete()
    if os.path.exists(zip_name): os.remove(zip_name)

@router.callback_query(F.data == "admin_back", AdminFilter())
async def admin_back_handler(callback: types.CallbackQuery, state: FSMContext = None):
    if state: await state.clear()
    await callback.message.edit_text("👑 <b>پنل مدیریت ربات</b>", reply_markup=await get_admin_keyboard()); await callback.answer()

# --- تیکتینگ پشتیبانی ---
@router.callback_query(F.data.startswith("ans_sup_"), AdminFilter())
async def reply_support_start(callback: types.CallbackQuery, state: FSMContext):
    parts = callback.data.split("_")
    ticket_id = int(parts[2])
    target_uid = int(parts[3])

    ticket = await db.get_support_ticket(ticket_id)
    if not ticket or ticket["status"] != "open":
        admin_who = ticket.get("answered_by") if ticket else "ادمین دیگری"
        await callback.answer(f"⚠️ این تیکت قبلاً توسط {admin_who} پاسخ داده شده است!", show_alert=True)
        return

    await state.update_data(ans_ticket_id=ticket_id, ans_target_uid=target_uid)
    await state.set_state(AdminState.waiting_for_support_reply)
    await callback.message.answer(f"✍️ لطفاً <b>متن پاسخ خود</b> برای کاربر (<code>{target_uid}</code>) را ارسال فرمایید:")
    await callback.answer()

@router.message(AdminState.waiting_for_support_reply, AdminFilter())
async def reply_support_send(message: types.Message, state: FSMContext):
    if db.is_cancel_message(message.text):
        await state.clear()
        await message.reply("❌ پاسخگویی لغو شد.")
        return

    data = await state.get_data()
    ticket_id = data["ans_ticket_id"]
    target_uid = data["ans_target_uid"]

    ticket = await db.get_support_ticket(ticket_id)
    if not ticket or ticket["status"] != "open":
        await message.reply(f"⚠️ این تیکت قبلاً توسط {ticket.get('answered_by', 'ادمین دیگری')} پاسخ داده شده است!")
        await state.clear()
        return

    admin_name = message.from_user.first_name or str(message.from_user.id)
    await db.resolve_support_ticket(ticket_id, admin_name)

    user_reply_text = f"💬 <b>پاسخ پشتیبانی به پیام شما:</b>\n\n{message.text}"

    try:
        await message.bot.send_message(chat_id=target_uid, text=user_reply_text)
        await message.reply(f"✅ <b>پاسخ شما با موفقیت برای کاربر ارسال شد.</b> (تیکت #{ticket_id} بسته شد)")
    except Exception as e:
        await message.reply(f"❌ خطا در ارسال پیام به کاربر: {e}")

    await state.clear()

# --- تایید/رد هوشمند واریز ---
@router.callback_query(F.data.startswith("app_dep_"), AdminFilter())
async def approve_deposit_handler(callback: types.CallbackQuery):
    dep_id = int(callback.data.split("_")[2])
    dep = await db.get_deposit_rec(dep_id)
    if not dep or dep["status"] != "pending":
        await callback.answer("⚠️ این رسید قبلاً تعیین تکلیف شده است!", show_alert=True)
        return

    admin_name = callback.from_user.first_name or str(callback.from_user.id)
    await db.resolve_deposit_rec(dep_id, "approved", admin_name)
    await db.update_balance(dep["user_id"], dep["amount"])

    new_caption = (callback.message.caption or "") + f"\n\n🟢 <b>تایید شد توسط: {admin_name}</b>\n💰 مبلغ {dep['amount']:,} ت واریز گردید."
    await callback.message.edit_caption(caption=new_caption, reply_markup=None)
    try:
        await db.send_bot_sticker(callback.bot, dep["user_id"], "sticker_success")
        await callback.bot.send_message(chat_id=dep["user_id"], text=f"🎉 واریزی شما به مبلغ <b>{dep['amount']:,} تومان</b> تایید و به موجودی شما افزوده شد.")
    except Exception: pass
    await callback.answer("شارژ انجام شد.")

@router.callback_query(F.data.startswith("rej_dep_"), AdminFilter())
async def reject_deposit_handler(callback: types.CallbackQuery):
    dep_id = int(callback.data.split("_")[2])
    dep = await db.get_deposit_rec(dep_id)
    if not dep or dep["status"] != "pending":
        await callback.answer("⚠️ این رسید قبلاً تعیین تکلیف شده است!", show_alert=True)
        return

    admin_name = callback.from_user.first_name or str(callback.from_user.id)
    await db.resolve_deposit_rec(dep_id, "rejected", admin_name)
    new_caption = (callback.message.caption or "") + f"\n\n🔴 <b>رد شد توسط: {admin_name}</b>"
    await callback.message.edit_caption(caption=new_caption, reply_markup=None)
    try: await callback.bot.send_message(chat_id=dep["user_id"], text="❌ رسید واریزی شما تایید نشد.")
    except Exception: pass
    await callback.answer("رسید رد شد.")

# --- تایید/رد هوشمند KYC ---
@router.callback_query(F.data.startswith("app_kyc_"), AdminFilter())
async def approve_kyc_safe_handler(callback: types.CallbackQuery):
    sub_id = int(callback.data.split("_")[2])
    sub = await db.get_kyc_sub(sub_id)
    if not sub or sub["status"] != "pending":
        await callback.answer("⚠️ این احراز هویت قبلاً بررسی شده است!", show_alert=True)
        return

    admin_name = callback.from_user.first_name or str(callback.from_user.id)
    await db.resolve_kyc_sub(sub_id, "approved", admin_name)
    await db.set_user_kyc(sub["user_id"], 2, sub["card_number"])

    new_caption = (callback.message.caption or "") + f"\n\n🟢 <b>تایید شد توسط: {admin_name}</b>"
    await callback.message.edit_caption(caption=new_caption, reply_markup=None)
    try:
        await db.send_bot_sticker(callback.bot, sub["user_id"], "sticker_success")
        await callback.bot.send_message(chat_id=sub["user_id"], text="🎉 <b>تبریک! مدارک احراز هویت شما با موفقیت تایید شد.</b>\nاکنون می‌توانید نسبت به افزایش موجودی و خرید اقدام فرمایید.")
    except Exception: pass
    await callback.answer("احراز هویت تایید شد.")

@router.callback_query(F.data.startswith("rej_kyc_"), AdminFilter())
async def reject_kyc_safe_handler(callback: types.CallbackQuery):
    sub_id = int(callback.data.split("_")[2])
    sub = await db.get_kyc_sub(sub_id)
    if not sub or sub["status"] != "pending":
        await callback.answer("⚠️ این احراز هویت قبلاً بررسی شده است!", show_alert=True)
        return

    admin_name = callback.from_user.first_name or str(callback.from_user.id)
    await db.resolve_kyc_sub(sub_id, "rejected", admin_name)
    await db.set_user_kyc(sub["user_id"], 0)

    new_caption = (callback.message.caption or "") + f"\n\n🔴 <b>رد شد توسط: {admin_name}</b>"
    await callback.message.edit_caption(caption=new_caption, reply_markup=None)
    try: await callback.bot.send_message(chat_id=sub["user_id"], text="❌ <b>متاسفانه مدارک احراز هویت شما تایید نشد.</b>\nلطفاً مجدداً ارسال فرمایید.")
    except Exception: pass
    await callback.answer("احراز رد شد.")

@router.callback_query(F.data.startswith("order_done_"), AdminFilter())
async def order_done(callback: types.CallbackQuery):
    oid = int(callback.data.split("_")[2])
    o = await db.get_order(oid)
    if not o or o["status"] != "pending":
        await callback.answer("⚠️ این سفارش قبلاً تعیین تکلیف شده است!", show_alert=True)
        return

    admin_name = callback.from_user.first_name or str(callback.from_user.id)
    await db.update_order_status(oid, "completed")
    
    new_text = (callback.message.text or "") + f"\n\n🟢 <b>سفارش تحویل داده شد توسط: {admin_name}</b>"
    await callback.message.edit_text(new_text, reply_markup=None)
    try:
        await db.send_bot_sticker(callback.bot, o["user_id"], "sticker_success")
        await callback.bot.send_message(chat_id=o["user_id"], text=f"🎉 سفارش <code>#{oid}</code> شما با موفقیت تحویل داده شد.")
    except Exception: pass
    await callback.answer("سفارش تایید شد.")

@router.callback_query(F.data.startswith("order_cancel_"), AdminFilter())
async def order_cancel(callback: types.CallbackQuery):
    oid = int(callback.data.split("_")[2])
    o = await db.get_order(oid)
    if not o or o["status"] != "pending":
        await callback.answer("⚠️ این سفارش قبلاً تعیین تکلیف شده است!", show_alert=True)
        return

    admin_name = callback.from_user.first_name or str(callback.from_user.id)
    await db.update_order_status(oid, "cancelled")
    await db.update_balance(o["user_id"], o["amount_paid"])
    
    new_text = (callback.message.text or "") + f"\n\n🔴 <b>سفارش لغو و وجه برگشت داده شد توسط: {admin_name}</b>"
    await callback.message.edit_text(new_text, reply_markup=None)
    try: await callback.bot.send_message(chat_id=o["user_id"], text=f"⚠️ سفارش <code>#{oid}</code> لغو شد و مبلغ به کیف پول برگشت داده شد.")
    except Exception: pass
    await callback.answer("سفارش لغو شد.")
EOF
```

---

دستور بالا رو اجرا و ری‌استارت کن. الان:
1. اگر بسته‌های استارز رو حذف کنی، بعد از ری‌استارت دیگه هرگز برنمی‌گردن!
2. تمام دکمه‌های ادمین رنگی (`style="..."`) هستند.
3. دکمه قرمز رنگ **`🪪 لغو / ابطال KYC کاربر`** به پنل ادمین اضافه شده و آماده تسته!

تغییر بعدی رو بفرست تا ادامه بدیم!
