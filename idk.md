دقیقاً دست گذاشتی روی باگ داداش! متوجه شدم چرا این اتفاق افتاد:

### 🔍 علت پاک شدن استیکر ❄️:
توی ریجکس قبلی، کاراکتر `❄️` داخل لیست نمادهای پاک‌کننده دور ساعت گذاشته شده بود؛ برای همین وقتی ساعت رو عوض می‌کردی، سیستم فکر می‌کرد اون `❄️` آخر اسمت کادر ساعت قبلی بوده و پاکش می‌کرد!

### 🛠 نحوه حل مشکل:
* تمام ایموجی‌ها (مثل ❄️، 🔥، 💎، ⚡️ و...) از فیلتر پاک‌سازی کادر **به طور کامل حذف شدند**.
* الان الگوریتم فقط کادرهای واقعی (مثل `𓆩 𓆪` یا `亗 亗`) رو دور ساعت عوض می‌کنه و **متن و استیکرهای قبل و بعد اسمت (مثل `❄️` اول و `❄️` آخر) ۱۰۰٪ دست‌نخورده و سالم می‌مونن**.

نتیجه دقیقاً میشه:
👉 **`❄️ Frozen Mamad 𓆩۲۰:۴۴𓆪 ❄️`**

---

### 🚀 دستور لینوکسی فیکس نهایی:

این دستور رو کامل داخل سرورت پیست و اجرا کن:

```bash
cat << 'EOF' > database.py
import aiosqlite, re
from typing import Optional, Dict, Any, List
from config import config

CACHE_EMOJIS = {}
REVERSE_EMOJIS = {}

ALL_DIGITS_STR = "0123456789۰۱۲۳۴۵۶۷۸۹٠١٢٣٤٥٦٧٨٩𝟘𝟙𝟚𝟛𝟜𝟝𝟞𝟟𝟠𝟡⁰¹²³⁴⁵⁶⁷⁸⁹₀₁₂₃₄₅₆₇₈₉𝟎𝟏𝟐𝟑𝟒𝟓𝟔𝟕𝟖𝟗𝟬𝟭𝟮𝟯𝟰𝟱𝟲𝟳𝟴𝟵𝟢𝟣𝟤𝟥𝟦𝟧𝟨𝟩𝟪𝟫𝟷𝟸𝟹𝟺𝟻𝟼𝟽𝟾𝟿⓿➊➋➌➍➎➏➐➑➒⓪①②③④⑤⑥⑦⑧⑨🄋➀➁➂➃➄➅➆➇➈⒪⑴⑵⑶⑷⑸⑹⑺⑻⑼🄀⒈⒉⒊⒋⒌⒍⒎⒏⒐🄌➊➋➌➍➎➏➐➑➒０１２３４５６７８９🯰🯱🯲𝯳𝯴𝯵𝯶𝯷𝯸𝯹ⅠⅡⅢⅣⅤⅥⅦⅧⅨ❶❷❸❹❺❻❼❽❾⠚⠁⠃⠉⠙⠑⠋⠛⠓⠊०१२३४५६७८९০১২৩৪৫৬৭৮৯๐๑๒๓๔๕๖๗๘๙༠༡༢༣༤༥༦༧༨༩០១២៣៤۵۶۷۸۹၀၁၂၃၄၅၆၇၈၉੦੧੨੩੪੫੬੭੮੯೦೧೨೩೪೫೬೭೮೯൦൧൨൩൪൫൬൭൮൯௦௧௨௩௪௫௬௭௮௯౦౧౨౩౪౫౬౭౮౯૦૧૨૩૪૫૬૭૮૯୦୧୨୩୪୫୬୭୮୯෦෧෨෩෪෫෬෭෮෯໐໑໒໓໔໕໖໗໘໙"
BRACKET_SYMBOLS = r"﹝﹞❲❳‹›「」【】〖〗⟦⟧⦅⦆﹤﹥⟮⟯⟬⟭❨❩❮❯«»𓆩𓆪⸢⸥⌜⌟☽☾✦✧⌁亗༒⊶⊷༺༻⧉⟡|•\(\)\[\]"

def clean_time_from_string(text: str) -> str:
    if not text:
        return ""
    # پاک‌سازی فقط ارقام ساعت و کادرهای بلافاصله چسبیده به آن بدون دست زدن به ایموجی‌های اسم کاربر
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
            dst_ttl TEXT DEFAULT 'me', dst_del TEXT DEFAULT 'me', dst_edit TEXT DEFAULT 'me', dst_pv TEXT DEFAULT 'me',
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
            ("save_pv", "INTEGER DEFAULT 0"), ("dst_ttl", "TEXT DEFAULT 'me'"),
            ("dst_del", "TEXT DEFAULT 'me'"), ("dst_edit", "TEXT DEFAULT 'me'"),
            ("dst_pv", "TEXT DEFAULT 'me'")
        ]
        for c, t in self_cols:
            await add_col(db, "self_bots", c, t)

        await db.execute("INSERT OR IGNORE INTO admins (user_id) VALUES (?)", (config.ADMIN_ID,))
        
        defaults = {
            "card_number": "6037-9900-0000-0000", "card_holder": "نام صاحب حساب",
            "welcome_text": "سلام {name} عزیز، به ربات خوش آمدید!\n\nاز منوی زیر استفاده کنید:",
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

        if (await (await db.execute("SELECT COUNT(*) FROM custom_gifts")).fetchone())[0] == 0:
            for g_name, g_stars, g_price in [("خرس تدی 🧸", 100, 150000), ("قلب سرخ ❤️", 50, 100000), ("الماس درخشان 💎", 250, 250000)]:
                await db.execute("INSERT INTO custom_gifts (name, stars_cost, price) VALUES (?, ?, ?)", (g_name, g_stars, g_price))

        if (await (await db.execute("SELECT COUNT(*) FROM stars_packages")).fetchone())[0] == 0:
            for title, amt, prc in [("⭐️ ۵۰ استارز", 50, 75000), ("⭐️ ۱۰۰ استارز", 100, 145000), ("⭐️ ۲۵۰ استارز", 250, 355000), ("⭐️ ۵۰۰ استارز", 500, 695000), ("⭐️ ۱۰۰۰ استارز", 1000, 1380000)]:
                await db.execute("INSERT INTO stars_packages (btn_title, stars_amount, price) VALUES (?, ?, ?)", (title, amt, prc))

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

async def set_user_kyc(uid, status, card=None):
    async with aiosqlite.connect(config.DB_NAME) as db:
        is_v = 1 if status == 2 else 0
        if card: await db.execute("UPDATE users SET kyc_status = ?, is_verified = ?, verified_card = ? WHERE user_id = ?", (status, is_v, card, uid))
        else: await db.execute("UPDATE users SET kyc_status = ?, is_verified = ? WHERE user_id = ?", (status, is_v, uid))
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

cat << 'EOF' > handlers/helper_bot.py
import uuid
import re
import datetime
from zoneinfo import ZoneInfo
from aiogram import Router, types, F, Bot
from aiogram.types import (
    InlineQuery, 
    InlineQueryResultArticle, 
    InputTextMessageContent,
    InlineKeyboardMarkup, 
    InlineKeyboardButton
)
from aiogram.enums import ParseMode
import database as db

router = Router()

# ۳۶ فریم و کادر اختصاصی بدون هیچ تداخل با ایموجی‌های اسم
CLOCK_FRAMES = [
    "﹝{clock}﹞",
    "❲{clock}❳",
    "‹{clock}›",
    "「{clock}」",
    "【{clock}】",
    "〖{clock}〗",
    "⟦{clock}⟧",
    "⦅{clock}⦆",
    "﹤{clock}﹥",
    "⟮{clock}⟯",
    "⟬{clock}⟭",
    "❨{clock}❩",
    "❮{clock}❯",
    "«{clock}»",
    "𓆩{clock}𓆪",
    "⸢{clock}⸥",
    "⌜{clock}⌟",
    "☽{clock}☾",
    "✦{clock}✦",
    "✧{clock}✧",
    "⌁{clock}⌁",
    "亗{clock}亗",
    "༒{clock}༒",
    "⊶{clock}⊷",
    "༺{clock}༻",
    "⧉{clock}⧉",
    "⟡{clock}⟡",
    "|{clock}|",
    "•{clock}•",
    "[{clock}]",
    "({clock})",
    "<{clock}>",
    "« {clock} »",
    "• {clock} •",
    "| {clock} |",
    "{clock}"
]

FONT_LIST = [
    ("persian", "فارسی کلاسیک", "۱۸:۳۰"),
    ("digital", "دیجیتال توخالی", "𝟙𝟠:𝟛𝟘"),
    ("superscript", "بالانویس", "¹⁸:³⁰"),
    ("subscript", "پایین‌نویس", "₁₈:₃₀"),
    ("bold", "بولد کلاسیک", "𝟏𝟖:𝟑𝟎"),
    ("sans_bold", "سانس بولد", "𝟭𝟴:𝟯𝟬"),
    ("sans", "سانس ساده", "𝟣𝟪:𝟥𝟢"),
    ("monospace", "مونو ماشین‌تحریر", "𝟷𝟾:𝟹𝟶"),
    ("circled_dark", "دایره مشکی", "➊➑:➌⓿"),
    ("circled_white", "دایره سفید", "①⑧:③⓪"),
    ("circled_serif", "دایره حاشیه‌دار", "➀➇:➂🄋"),
    ("parenthesized", "پرانتزی", "⑴⑻:⑶⒪"),
    ("dotted", "نقطه‌دار لوکس", "⒈⒏:⒊🄀"),
    ("square_dark", "مربعی توپر", "➊➑:➌🄌"),
    ("fullwidth", "عریض ژاپنی", "１８：３０"),
    ("segment_7", "ساعت مچی ۷-سگمنت", "🯱🯸:𝯳🯰"),
    ("arabic", "عربی سنتی", "١٨:٣٠"),
    ("roman", "رومی باستان", "ⅠⅧ:Ⅲ0"),
    ("bubble_black", "حباب مشکی", "❶❽:❸⓿"),
    ("braille", "خط بریل", "⠁⠓:⠉⠚"),
    ("devanagari", "هندی سانسکریت", "१८:३०"),
    ("bengali", "بنگالی آرتیستیک", "১৮:৩০"),
    ("thai", "تایلندی مینیمال", "๑๘:๓๐"),
    ("tibetan", "تبتی ماورایی", "༡༨:༣༠"),
    ("khmer", "خمری سلطنتی", "១៨:៣០"),
    ("myanmar", "میانمار کهکشانی", "၁၈:၃၀"),
    ("gurmukhi", "گورموکی هندی", "੧੮:੩੦"),
    ("kannada", "کانادا طلایی", "೧೮:೩೦"),
    ("malayalam", "مالایالام امواج", "൧൮:൩൦"),
    ("tamil", "تامیل کلاسیک", "௧௮:௩௦"),
    ("telugu", "تلوگو فانتزی", "౧౮:౩౦"),
    ("gujarati", "گجراتی لوکس", "૧૮:૩૦"),
    ("oriya", "اوریا خاص", "୧୮:୩୦"),
    ("sinhala", "سینهالی باستانی", "෧෮:෩෦"),
    ("lao", "لائوسی مدرن", "໑໘:໓໐"),
    ("regular", "انگلیسی ساده", "18:30")
]

async def edit_panel_message(callback: types.CallbackQuery, text: str, reply_markup: InlineKeyboardMarkup = None):
    if callback.inline_message_id:
        await callback.bot.edit_message_text(
            inline_message_id=callback.inline_message_id,
            text=text,
            reply_markup=reply_markup,
            parse_mode=ParseMode.HTML
        )
    elif callback.message:
        await callback.message.edit_text(
            text=text,
            reply_markup=reply_markup,
            parse_mode=ParseMode.HTML
        )

def get_main_categories_markup(owner_id: int) -> InlineKeyboardMarkup:
    buttons = [
        [
            InlineKeyboardButton(text="✍️ فرمت خودکار متن", style="primary", callback_data=f"cat_format_{owner_id}"),
            InlineKeyboardButton(text="👤 پروفایل و ساعت", style="primary", callback_data=f"cat_profile_{owner_id}")
        ],
        [
            InlineKeyboardButton(text="💾 ذخیره‌ساز و ضدکپی", style="primary", callback_data=f"cat_saver_{owner_id}"),
            InlineKeyboardButton(text="🔔 ری‌اکشن خودکار", style="primary", callback_data=f"cat_react_{owner_id}")
        ],
        [
            InlineKeyboardButton(text="🤔 منشی و حالت AFK", style="primary", callback_data=f"cat_afk_{owner_id}"),
            InlineKeyboardButton(text="📱 اسپمر و ارسال مکرر", style="primary", callback_data=f"cat_spam_{owner_id}")
        ],
        [
            InlineKeyboardButton(text="💬 پاسخگوی خودکار", style="primary", callback_data=f"cat_autoresp_{owner_id}"),
            InlineKeyboardButton(text="🎵 موزیک‌یاب آنلاین", style="primary", callback_data=f"cat_music_{owner_id}")
        ],
        [
            InlineKeyboardButton(text="🛠️ جعبه ابزار و تست", style="primary", callback_data=f"cat_tools_{owner_id}")
        ],
        [
            InlineKeyboardButton(text="❌ بستن پنل", style="danger", callback_data=f"cat_close_{owner_id}")
        ],
        [
            InlineKeyboardButton(text="⚡️ Powered by PuLaBoX", callback_data="act_ignore")
        ]
    ]
    return InlineKeyboardMarkup(inline_keyboard=buttons)

@router.callback_query(F.data == "act_ignore")
async def ignore_callback(callback: types.CallbackQuery):
    await callback.answer()

@router.inline_query()
async def helper_inline_panel_handler(query: InlineQuery, bot: Bot):
    owner_id = query.from_user.id
    if query.query.startswith("panel_"):
        parts = query.query.split("_")
        if len(parts) > 1 and parts[1].isdigit():
            owner_id = int(parts[1])

    self_data = await db.get_self_bot(owner_id)
    if not self_data or self_data.get("is_active", 0) != 1:
        return

    text_panel = (
        f"👑 <b>کنترل پنل اختصاصی سلف‌بات PuLaSeLf</b>\n"
        f"👤 کاربر: <b>{query.from_user.full_name}</b>\n"
        f"📡 وضعیت سلف: <code>متصل و آنلاین 🟢</code>\n\n"
        "👇 <b>جهت دسترسی به تمام ابزارها و دکمه‌های شیشه‌ای، بخش مورد نظر را انتخاب کنید:</b>"
    )

    result = InlineQueryResultArticle(
        id=str(uuid.uuid4()),
        title="👑 باز کردن کنترل پنل شیشه‌ای PuLaSeLf",
        description="مدیریت کامل ساعت اسم، بیوگرافی، آنتی‌دلیت، اسپمر، پاسخ خودکار و ابزارها",
        input_message_content=InputTextMessageContent(
            message_text=text_panel,
            parse_mode=ParseMode.HTML
        ),
        reply_markup=get_main_categories_markup(owner_id)
    )
    await query.answer(results=[result], cache_time=1, is_personal=True)

@router.callback_query(F.data.startswith("cat_"))
async def category_router_callback(callback: types.CallbackQuery):
    parts = callback.data.split("_")
    category = parts[1]
    owner_id = int(parts[2])

    if callback.from_user.id != owner_id:
        await callback.answer("⛔️ این کنترل پنل منحصراً مخصوص صاحب سلف است!", show_alert=True)
        return

    self_data = await db.get_self_bot(owner_id)
    if not self_data:
        await callback.answer("⚠️ سلف‌بات شما در حال حاضر فعال نیست!", show_alert=True)
        return

    if category == "saver":
        st_ttl = "🟢 فعال" if self_data.get("save_ttl", 1) else "🔴 غیرفعال"
        st_del = "🟢 فعال" if self_data.get("anti_delete", 1) else "🔴 غیرفعال"
        st_edit = "🟢 فعال" if self_data.get("anti_edit", 1) else "🔴 غیرفعال"
        st_pv = "🟢 فعال" if self_data.get("save_pv", 0) else "🔴 غیرفعال"

        text = (
            "💾 <b>مدیریت ذخیره‌ساز، عکس تایمردار و ضدکپی:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"🔒 <b>عکس و رسانه‌های تایمردار:</b> <b>{st_ttl}</b>\n"
            f"🗑️ <b>آنتی‌دلیت پیام‌های پیوی:</b> <b>{st_del}</b>\n"
            f"✏️ <b>آنتی‌ادیت پیام‌های پیوی:</b> <b>{st_edit}</b>\n"
            f"📥 <b>سیو خودکار پیام‌های پیوی:</b> <b>{st_pv}</b>\n\n"
            "💡 <b>دستورات متنی سریع:</b>\n"
            "• <code>سیو</code> (روی ریپلای یا با لینک) ➔ کپی در Saved Messages\n"
            "• <code>فور</code> (روی ریپلای یا با لینک) ➔ ساخت لینک فوروارد"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🔒 عکس تایمردار: تغییر وضعیت", style="primary", callback_data=f"act_tog_ttl_{owner_id}")],
            [InlineKeyboardButton(text="🗑️ آنتی‌دلیت پیوی: تغییر وضعیت", style="primary", callback_data=f"act_tog_del_{owner_id}")],
            [InlineKeyboardButton(text="✏️ آنتی‌ادیت پیوی: تغییر وضعیت", style="primary", callback_data=f"act_tog_edit_{owner_id}")],
            [InlineKeyboardButton(text="📥 سیو پیام‌های پیوی: تغییر وضعیت", style="primary", callback_data=f"act_tog_pv_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "format":
        mode = self_data.get("auto_format_mode", "none")
        mode_fa = {
            "bold": "🟢 بولد خودکار (Bold)",
            "italic": "🟢 ایتالیک خودکار (Italic)",
            "mono": "🟢 کد/مونو خودکار (Monospace)",
            "none": "🔴 غیرفعال"
        }.get(mode, "🔴 غیرفعال")

        text = (
            "✍️ <b>مدیریت فرمت خودکار پیام‌های ارسالی:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"⚙️ <b>وضعیت فعال:</b> <b>{mode_fa}</b>\n\n"
            "💡 <i>با فعال‌سازی هر حالت، تمامی پیام‌های ارسالی شما با همان استایل ارسال می‌شوند.</i>\n\n"
            "دستورات متنی: `بولد روشن/خاموش` | `ایتالیک روشن/خاموش` | `مونو روشن/خاموش`"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🔘 فعال‌سازی: بولد خودکار", style="success", callback_data=f"act_setmode_bold_{owner_id}")],
            [InlineKeyboardButton(text="🔘 فعال‌سازی: ایتالیک خودکار", style="success", callback_data=f"act_setmode_italic_{owner_id}")],
            [InlineKeyboardButton(text="🔘 فعال‌سازی: کد/مونو خودکار", style="success", callback_data=f"act_setmode_mono_{owner_id}")],
            [InlineKeyboardButton(text="🔴 غیرفعال‌سازی فرمت خودکار", style="danger", callback_data=f"act_setmode_none_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "profile":
        c_name = "🟢 روشن" if self_data.get("clock_name") else "🔴 خاموش"
        c_bio = "🟢 روشن" if self_data.get("clock_bio") else "🔴 خاموش"
        tmpl = self_data.get("name_template") or "User {clock}"
        font_txt = self_data.get("clock_font", "persian")

        text = (
            "👤 <b>مدیریت ساعت زنده پروفایل و بیوگرافی:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"🕒 <b>ساعت روی اسم:</b> <b>{c_name}</b>\n"
            f"📝 <b>ساعت روی بیو:</b> <b>{c_bio}</b>\n"
            f"🎨 <b>فونت فعال اعداد:</b> <code>{font_txt}</code>\n"
            f"🏷 <b>الگوی فعال اسم:</b> <code>{tmpl}</code>\n\n"
            "━━━━━━━━━━━━━━━━━━━━\n"
            "📝 <b>آموزش تغییر دستی اسم و محل ساعت:</b>\n"
            "کافیست در هر چتی بنویسید:\n"
            "• <code>تنظیم اسم نام‌شما {clock}</code>\n"
            "• <code>.setname Ali {clock}</code> (ساعت در آخر اسم)\n"
            "• <code>.setname {clock} Ali</code> (ساعت در اول اسم)\n"
            "• <code>حذف ساعت اسم</code> یا <code>.resetname</code> (حذف ساعت)\n\n"
            "👇 <b>جهت تعویض فریم ساعت یا فونت اعداد از دکمه‌های زیر استفاده کنید:</b>"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🎨 ۳۶ کادر و فریم ساعت اسم", style="success", callback_data=f"act_tmpl_list_1_{owner_id}")],
            [InlineKeyboardButton(text="🔢 ۳۶ فونت زنده اعداد ساعت", style="success", callback_data=f"act_font_list_1_{owner_id}")],
            [InlineKeyboardButton(text="🕒 تغییر وضعیت ساعت اسم", style="primary", callback_data=f"act_toggle_name_{owner_id}")],
            [InlineKeyboardButton(text="📝 تغییر وضعیت ساعت بیوگرافی", style="primary", callback_data=f"act_toggle_bio_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "afk":
        afk_st = "🟢 روشن (پاسخ خودکار فعال)" if self_data.get("afk_status") else "🔴 خاموش"
        reason = self_data.get("afk_reason") or "در دسترس نیستم"
        text = (
            "🤔 <b>مدیریت منشی خودکار و حالت استراحت (AFK):</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"💤 <b>وضعیت منشی:</b> <b>{afk_st}</b>\n"
            f"📝 <b>متن پاسخ خودکار:</b> <i>{reason}</i>\n\n"
            "💡 <b>دستورات متنی:</b>\n"
            "• <code>اف کی [دلیل]</code> ➔ فعال‌سازی\n"
            "• <code>خروج اف کی</code> ➔ غیرفعال‌سازی"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="💤 روشن/خاموش کردن منشی AFK", style="primary", callback_data=f"act_toggle_afk_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "react":
        cur_react = await db.get_setting(f"self_react_{owner_id}", "off")
        text = (
            "🔔 <b>مدیریت ری‌اکشن خودکار روی پیام‌ها:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"ایموجی فعال فعلی: <b>{cur_react}</b>\n\n"
            "روی هر ایموجی بزنید تا ری‌اکشن خودکار روی آن تنظیم شود:"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [
                InlineKeyboardButton(text="❤️ قلب", style="primary", callback_data=f"act_react_heart_{owner_id}"),
                InlineKeyboardButton(text="🔥 آتش", style="primary", callback_data=f"act_react_fire_{owner_id}"),
                InlineKeyboardButton(text="👍 لایک", style="primary", callback_data=f"act_react_like_{owner_id}")
            ],
            [
                InlineKeyboardButton(text="👏 تشویق", style="primary", callback_data=f"act_react_clap_{owner_id}"),
                InlineKeyboardButton(text="😂 خنده", style="primary", callback_data=f"act_react_laugh_{owner_id}"),
                InlineKeyboardButton(text="⚡️ رعد", style="primary", callback_data=f"act_react_bolt_{owner_id}")
            ],
            [InlineKeyboardButton(text="🔴 غیرفعال‌سازی ری‌اکشن خودکار", style="danger", callback_data=f"act_react_off_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "spam":
        text = (
            "📱 <b>مدیریت اسپمر و ارسال مکرر پیام:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            "💡 <b>الگوهای متنی اسپمر:</b>\n"
            "• <code>اسپم 100 بار هر ۲ ثانیه سلام</code>\n"
            "• <code>اسپم 20 بار هر 1 دقیقه متن</code>\n"
            "• <code>.spam 10 1 text</code>\n\n"
            "🛑 <b>توقف فوری:</b> دستور <code>توقف اسپم</code> یا دکمه زیر:"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🛑 توقف فوری تمامی اسپم‌ها", style="danger", callback_data=f"act_stopspam_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "autoresp":
        raw = await db.get_setting(f"autoans_{owner_id}", "{}")
        try: ans_dict = json.loads(raw)
        except Exception: ans_dict = {}

        text = "💬 <b>پاسخ‌دهنده خودکار کلمات (Auto-Answer):</b>\n━━━━━━━━━━━━━━━━━━━━\n\n"
        if ans_dict:
            text += "📋 <b>لیست کلمات ثبت‌شده:</b>\n"
            for idx, (k, v) in enumerate(ans_dict.items(), 1):
                text += f"{idx}. <code>{k}</code> ➔ <i>{v}</i>\n"
        else:
            text += "<i>هیچ کلمه‌ای هنوز ثبت نشده است.</i>\n"

        text += (
            "\n💡 <b>دستورات متنی:</b>\n"
            "• افزودن: <code>پاسخ اضافه کلمه | جواب</code>\n"
            "• حذف: <code>پاسخ حذف کلمه</code>\n"
            "• لیست: <code>پاسخ لیست</code>"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "music":
        text = (
            "🎵 <b>موزیک‌یاب آنلاین و پیشرفته:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            "🔍 <b>دستورات جستجو و دانلود موزیک:</b>\n"
            "• <code>موزیک [نام آهنگ یا خواننده]</code>\n"
            "• <code>آهنگ [نام موزیک]</code>\n"
            "• <code>.find [نام موزیک]</code>\n\n"
            "💡 مثال: <code>موزیک شادمهر عقیلی</code>"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "tools":
        text = (
            "🛠️ <b>جعبه ابزار و تست‌های کاربردی:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            "• <code>پینگ</code> ➔ تست پینگ لحظه‌ای\n"
            "• <code>ساعت</code> و <code>تاریخ</code> ➔ زمان دقیق تهران\n"
            "• <code>حساب 2+2*5</code> ➔ ماشین‌حساب ریاضی\n"
            "• <code>آمار چت</code> ➔ آنالیز ۱۰۰ پیام اخیر چت\n"
            "• <code>اطلاعات</code> (ریپلای/آیدی) ➔ مشخصات کاربر و دانلود پروفایل\n"
            "• <code>تگ همه [متن]</code> ➔ منشن کردن همه اعضای گروه\n"
            "• <code>حذف</code> (روی ریپلای) ➔ پاک کردن پیام\n"
            "• <code>ادیت [متن]</code> (روی ریپلای) ➔ ادیت فوری پیام شما"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="⚡️ تست پینگ زنده", style="success", callback_data=f"act_ping_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        await callback.answer()

    elif category == "close":
        if callback.inline_message_id:
            await callback.bot.edit_message_text(
                inline_message_id=callback.inline_message_id,
                text="❌ <b>کنترل پنل سلف‌بات بسته شد.</b>",
                reply_markup=None,
                parse_mode=ParseMode.HTML
            )
        elif callback.message:
            await callback.message.delete()
        await callback.answer("پنل بسته شد.")

@router.callback_query(F.data.startswith("act_"))
async def actions_callback(callback: types.CallbackQuery):
    if callback.data == "act_ignore":
        await callback.answer()
        return

    parts = callback.data.split("_")
    action = parts[1]

    # ۱. بخش انتخاب ۳۶ فریم و کادر دور ساعت
    if action == "tmpl":
        sub = parts[2]
        if sub == "list":
            page = int(parts[3])
            owner_id = int(parts[4])
            if callback.from_user.id != owner_id:
                await callback.answer("⛔️ دسترسی غیرمجاز!", show_alert=True)
                return

            self_data = await db.get_self_bot(owner_id)
            cur_tmpl = self_data.get("name_template") or ""
            raw_name = self_data.get("original_name") or callback.from_user.first_name or "User"
            clean_name = db.clean_time_from_string(raw_name) or "User"

            clean_base_tmpl = db.normalize_template_clock(cur_tmpl) if (cur_tmpl and "{clock}" in cur_tmpl) else f"{clean_name} {{clock}}"

            per_page = 6
            total_templates = len(CLOCK_FRAMES)
            total_pages = (total_templates + per_page - 1) // per_page
            start_idx = (page - 1) * per_page
            end_idx = min(start_idx + per_page, total_templates)

            kb_buttons = []
            for i in range(start_idx, end_idx):
                frame_str = CLOCK_FRAMES[i]
                preview_pattern = clean_base_tmpl.replace("{clock}", frame_str)
                btn_preview = preview_pattern.replace("{clock}", "18:30").replace("{name}", clean_name)
                kb_buttons.append([InlineKeyboardButton(text=f"{i+1}. {btn_preview}", style="primary", callback_data=f"act_applytmpl_{i}_{owner_id}")])

            nav_row = []
            if page > 1:
                nav_row.append(InlineKeyboardButton(text="⬅️ قبلی", style="primary", callback_data=f"act_tmpl_list_{page-1}_{owner_id}"))
            nav_row.append(InlineKeyboardButton(text=f"📄 {page} از {total_pages}", callback_data="act_ignore"))
            if page < total_pages:
                nav_row.append(InlineKeyboardButton(text="بعدی ➡️", style="primary", callback_data=f"act_tmpl_list_{page+1}_{owner_id}"))

            kb_buttons.append(nav_row)
            kb_buttons.append([InlineKeyboardButton(text="🔙 بازگشت به تنظیمات پروفایل", style="primary", callback_data=f"cat_profile_{owner_id}")])

            text = (
                "🎨 <b>انتخاب از ۳۶ کادر و فریم دور ساعت:</b>\n"
                "━━━━━━━━━━━━━━━━━━━━\n\n"
                f"🏷 <b>الگوی فعلی شما:</b> <code>{cur_tmpl or clean_base_tmpl}</code>\n\n"
                "💡 <i>کادر انتخابی دقیقاً دور ساعت شما قرار می‌گیرد و استیکرهای اسمتان دست‌نخورده باقی می‌ماند:</i>"
            )
            await edit_panel_message(callback, text, InlineKeyboardMarkup(inline_keyboard=kb_buttons))
            await callback.answer()
            return

    # ۲. بخش انتخاب ۳۶ فونت زنده اعداد ساعت
    elif action == "font":
        sub = parts[2]
        if sub == "list":
            page = int(parts[3])
            owner_id = int(parts[4])
            if callback.from_user.id != owner_id:
                await callback.answer("⛔️ دسترسی غیرمجاز!", show_alert=True)
                return

            per_page = 6
            total_fonts = len(FONT_LIST)
            total_pages = (total_fonts + per_page - 1) // per_page
            start_idx = (page - 1) * per_page
            end_idx = min(start_idx + per_page, total_fonts)

            kb_buttons = []
            for i in range(start_idx, end_idx):
                f_key, f_title, f_sample = FONT_LIST[i]
                kb_buttons.append([InlineKeyboardButton(text=f"{i+1}. {f_sample} ({f_title})", style="primary", callback_data=f"act_applyfont_{f_key}_{owner_id}")])

            nav_row = []
            if page > 1:
                nav_row.append(InlineKeyboardButton(text="⬅️ قبلی", style="primary", callback_data=f"act_font_list_{page-1}_{owner_id}"))
            nav_row.append(InlineKeyboardButton(text=f"📄 {page} از {total_pages}", callback_data="act_ignore"))
            if page < total_pages:
                nav_row.append(InlineKeyboardButton(text="بعدی ➡️", style="primary", callback_data=f"act_font_list_{page+1}_{owner_id}"))

            kb_buttons.append(nav_row)
            kb_buttons.append([InlineKeyboardButton(text="🔙 بازگشت به تنظیمات پروفایل", style="primary", callback_data=f"cat_profile_{owner_id}")])

            text = (
                "🔢 <b>انتخاب از ۳۶ فونت زنده اعداد ساعت:</b>\n"
                "━━━━━━━━━━━━━━━━━━━━\n\n"
                "روی هر فونت بزنید تا ارقام ساعت روی اسمتان با همان سبک نمایش داده شود:"
            )
            await edit_panel_message(callback, text, InlineKeyboardMarkup(inline_keyboard=kb_buttons))
            await callback.answer()
            return

    elif action == "applytmpl":
        tmpl_idx = int(parts[2])
        owner_id = int(parts[3])
        if callback.from_user.id != owner_id:
            await callback.answer("⛔️ دسترسی غیرمجاز!", show_alert=True)
            return

        self_data = await db.get_self_bot(owner_id)
        cur_tmpl = self_data.get("name_template") or ""
        raw_name = self_data.get("original_name") or callback.from_user.first_name or "User"
        clean_name = db.clean_time_from_string(raw_name) or "User"

        clean_base_tmpl = db.normalize_template_clock(cur_tmpl) if (cur_tmpl and "{clock}" in cur_tmpl) else f"{clean_name} {{clock}}"

        frame_str = CLOCK_FRAMES[tmpl_idx]
        new_template = clean_base_tmpl.replace("{clock}", frame_str)
        
        await db.update_self_bot(owner_id, name_template=new_template, original_name=clean_name, clock_name=1)
        await callback.answer(f"✅ فریم شماره {tmpl_idx+1} با موفقیت روی ساعت اعمال شد!", show_alert=True)
        
        self_data = await db.get_self_bot(owner_id)
        c_name = "🟢 روشن"
        c_bio = "🟢 روشن" if self_data.get("clock_bio") else "🔴 خاموش"
        tmpl = new_template
        font_txt = self_data.get("clock_font", "persian")

        text = (
            "👤 <b>مدیریت ساعت زنده پروفایل و بیوگرافی:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"🕒 <b>ساعت روی اسم:</b> <b>{c_name}</b>\n"
            f"📝 <b>ساعت روی بیو:</b> <b>{c_bio}</b>\n"
            f"🎨 <b>فونت فعال اعداد:</b> <code>{font_txt}</code>\n"
            f"🏷 <b>الگوی فعال اسم:</b> <code>{tmpl}</code>\n\n"
            "━━━━━━━━━━━━━━━━━━━━\n"
            "📝 <b>آموزش تغییر دستی اسم و محل ساعت:</b>\n"
            "کافیست در هر چتی بنویسید:\n"
            "• <code>تنظیم اسم نام‌شما {clock}</code>\n"
            "• <code>.setname Ali {clock}</code> (ساعت در آخر اسم)\n"
            "• <code>.setname {clock} Ali</code> (ساعت در اول اسم)\n"
            "• <code>حذف ساعت اسم</code> یا <code>.resetname</code> (حذف ساعت)\n\n"
            "👇 <b>جهت تعویض فریم ساعت یا فونت اعداد از دکمه‌های زیر استفاده کنید:</b>"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🎨 ۳۶ کادر و فریم ساعت اسم", style="success", callback_data=f"act_tmpl_list_1_{owner_id}")],
            [InlineKeyboardButton(text="🔢 ۳۶ فونت زنده اعداد ساعت", style="success", callback_data=f"act_font_list_1_{owner_id}")],
            [InlineKeyboardButton(text="🕒 تغییر وضعیت ساعت اسم", style="primary", callback_data=f"act_toggle_name_{owner_id}")],
            [InlineKeyboardButton(text="📝 تغییر وضعیت ساعت بیوگرافی", style="primary", callback_data=f"act_toggle_bio_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        return

    elif action == "applyfont":
        font_key = parts[2]
        owner_id = int(parts[3])
        if callback.from_user.id != owner_id:
            await callback.answer("⛔️ دسترسی غیرمجاز!", show_alert=True)
            return

        await db.update_self_bot(owner_id, clock_font=font_key)
        await callback.answer(f"✅ فونت اعداد ساعت به «{font_key}» تغییر یافت!", show_alert=True)

        self_data = await db.get_self_bot(owner_id)
        c_name = "🟢 روشن" if self_data.get("clock_name") else "🔴 خاموش"
        c_bio = "🟢 روشن" if self_data.get("clock_bio") else "🔴 خاموش"
        tmpl = self_data.get("name_template") or "User {clock}"
        font_txt = font_key

        text = (
            "👤 <b>مدیریت ساعت زنده پروفایل و بیوگرافی:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"🕒 <b>ساعت روی اسم:</b> <b>{c_name}</b>\n"
            f"📝 <b>ساعت روی بیو:</b> <b>{c_bio}</b>\n"
            f"🎨 <b>فونت فعال اعداد:</b> <code>{font_txt}</code>\n"
            f"🏷 <b>الگوی فعال اسم:</b> <code>{tmpl}</code>\n\n"
            "━━━━━━━━━━━━━━━━━━━━\n"
            "📝 <b>آموزش تغییر دستی اسم و محل ساعت:</b>\n"
            "کافیست در هر چتی بنویسید:\n"
            "• <code>تنظیم اسم نام‌شما {clock}</code>\n"
            "• <code>.setname Ali {clock}</code> (ساعت در آخر اسم)\n"
            "• <code>.setname {clock} Ali</code> (ساعت در اول اسم)\n"
            "• <code>حذف ساعت اسم</code> یا <code>.resetname</code> (حذف ساعت)\n\n"
            "👇 <b>جهت تعویض فریم ساعت یا فونت اعداد از دکمه‌های زیر استفاده کنید:</b>"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🎨 ۳۶ کادر و فریم ساعت اسم", style="success", callback_data=f"act_tmpl_list_1_{owner_id}")],
            [InlineKeyboardButton(text="🔢 ۳۶ فونت زنده اعداد ساعت", style="success", callback_data=f"act_font_list_1_{owner_id}")],
            [InlineKeyboardButton(text="🕒 تغییر وضعیت ساعت اسم", style="primary", callback_data=f"act_toggle_name_{owner_id}")],
            [InlineKeyboardButton(text="📝 تغییر وضعیت ساعت بیوگرافی", style="primary", callback_data=f"act_toggle_bio_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)
        return

    owner_id = int(parts[3]) if len(parts) > 3 else int(parts[2])
    if callback.from_user.id != owner_id:
        await callback.answer("⛔️ این دکمه منحصراً مخصوص صاحب سلف است!", show_alert=True)
        return

    if action == "back":
        caption = (
            f"👑 <b>کنترل پنل اختصاصی سلف‌بات PuLaSeLf</b>\n"
            f"👤 کاربر: <b>{callback.from_user.full_name}</b>\n"
            f"📡 وضعیت سلف: <code>متصل و آنلاین 🟢</code>\n\n"
            "👇 <b>جهت دسترسی به تنظیمات و ابزارها روی دسته‌بندی مورد نظر بزنید:</b>"
        )
        await edit_panel_message(callback, caption, get_main_categories_markup(owner_id))
        await callback.answer()

    elif action == "tog":
        sub = parts[2]
        self_data = await db.get_self_bot(owner_id)
        if sub == "ttl":
            n_val = 0 if self_data.get("save_ttl", 1) == 1 else 1
            await db.update_self_bot(owner_id, save_ttl=n_val)
            await callback.answer(f"🔒 ذخیره عکس تایمردار {'🟢 فعال' if n_val else '🔴 غیرفعال'} شد.", show_alert=True)
        elif sub == "del":
            n_val = 0 if self_data.get("anti_delete", 1) == 1 else 1
            await db.update_self_bot(owner_id, anti_delete=n_val)
            await callback.answer(f"🗑️ آنتی‌دلیت پیام پیوی {'🟢 فعال' if n_val else '🔴 غیرفعال'} شد.", show_alert=True)
        elif sub == "edit":
            n_val = 0 if self_data.get("anti_edit", 1) == 1 else 1
            await db.update_self_bot(owner_id, anti_edit=n_val)
            await callback.answer(f"✏️ آنتی‌ادیت پیام پیوی {'🟢 فعال' if n_val else '🔴 غیرفعال'} شد.", show_alert=True)
        elif sub == "pv":
            n_val = 0 if self_data.get("save_pv", 0) == 1 else 1
            await db.update_self_bot(owner_id, save_pv=n_val)
            await callback.answer(f"📥 سیو پیام‌های پیوی {'🟢 فعال' if n_val else '🔴 غیرفعال'} شد.", show_alert=True)

        self_data = await db.get_self_bot(owner_id)
        st_ttl = "🟢 فعال" if self_data.get("save_ttl", 1) else "🔴 غیرفعال"
        st_del = "🟢 فعال" if self_data.get("anti_delete", 1) else "🔴 غیرفعال"
        st_edit = "🟢 فعال" if self_data.get("anti_edit", 1) else "🔴 غیرفعال"
        st_pv = "🟢 فعال" if self_data.get("save_pv", 0) else "🔴 غیرفعال"

        text = (
            "💾 <b>مدیریت ذخیره‌ساز، عکس تایمردار و ضدکپی:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"🔒 <b>عکس و رسانه‌های تایمردار:</b> <b>{st_ttl}</b>\n"
            f"🗑️ <b>آنتی‌دلیت پیام‌های پیوی:</b> <b>{st_del}</b>\n"
            f"✏️ <b>آنتی‌ادیت پیام‌های پیوی:</b> <b>{st_edit}</b>\n"
            f"📥 <b>سیو خودکار پیام‌های پیوی:</b> <b>{st_pv}</b>\n\n"
            "💡 <b>دستورات متنی سریع:</b>\n"
            "• <code>سیو</code> (روی ریپلای یا با لینک) ➔ کپی در Saved Messages\n"
            "• <code>فور</code> (روی ریپلای یا با لینک) ➔ ساخت لینک فوروارد"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🔒 عکس تایمردار: تغییر وضعیت", style="primary", callback_data=f"act_tog_ttl_{owner_id}")],
            [InlineKeyboardButton(text="🗑️ آنتی‌دلیت پیوی: تغییر وضعیت", style="primary", callback_data=f"act_tog_del_{owner_id}")],
            [InlineKeyboardButton(text="✏️ آنتی‌ادیت پیوی: تغییر وضعیت", style="primary", callback_data=f"act_tog_edit_{owner_id}")],
            [InlineKeyboardButton(text="📥 سیو پیام‌های پیوی: تغییر وضعیت", style="primary", callback_data=f"act_tog_pv_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)

    elif action == "setmode":
        mode = parts[2]
        await db.update_self_bot(owner_id, auto_format_mode=mode)
        mode_fa = {"bold": "بولد خودکار", "italic": "ایتالیک خودکار", "mono": "کد/مونو خودکار", "none": "غیرفعال"}.get(mode, mode)
        await callback.answer(f"✅ فرمت خودکار به «{mode_fa}» تنظیم شد.", show_alert=True)
        
        self_data = await db.get_self_bot(owner_id)
        mode = self_data.get("auto_format_mode", "none")
        mode_fa = {
            "bold": "🟢 بولد خودکار (Bold)",
            "italic": "🟢 ایتالیک خودکار (Italic)",
            "mono": "🟢 کد/مونو خودکار (Monospace)",
            "none": "🔴 غیرفعال"
        }.get(mode, "🔴 غیرفعال")

        text = (
            "✍️ <b>مدیریت فرمت خودکار پیام‌های ارسالی:</b>\n"
            "━━━━━━━━━━━━━━━━━━━━\n\n"
            f"⚙️ <b>وضعیت فعال:</b> <b>{mode_fa}</b>\n\n"
            "💡 <i>با فعال‌سازی هر حالت، تمامی پیام‌های ارسالی شما با همان استایل ارسال می‌شوند.</i>\n\n"
            "دستورات متنی: `بولد روشن/خاموش` | `ایتالیک روشن/خاموش` | `مونو روشن/خاموش`"
        )
        kb = InlineKeyboardMarkup(inline_keyboard=[
            [InlineKeyboardButton(text="🔘 فعال‌سازی: بولد خودکار", style="success", callback_data=f"act_setmode_bold_{owner_id}")],
            [InlineKeyboardButton(text="🔘 فعال‌سازی: ایتالیک خودکار", style="success", callback_data=f"act_setmode_italic_{owner_id}")],
            [InlineKeyboardButton(text="🔘 فعال‌سازی: کد/مونو خودکار", style="success", callback_data=f"act_setmode_mono_{owner_id}")],
            [InlineKeyboardButton(text="🔴 غیرفعال‌سازی فرمت خودکار", style="danger", callback_data=f"act_setmode_none_{owner_id}")],
            [InlineKeyboardButton(text="🔙 بازگشت به منوی اصلی", style="primary", callback_data=f"act_back_home_{owner_id}")]
        ])
        await edit_panel_message(callback, text, kb)

    elif action == "toggle":
        sub_act = parts[2]
        self_data = await db.get_self_bot(owner_id)
        if sub_act == "name":
            new_val = 0 if self_data.get("clock_name") else 1
            await db.update_self_bot(owner_id, clock_name=new_val)
            await callback.answer(f"🕒 ساعت اسم {'🟢 روشن' if new_val else '🔴 خاموش'} شد.", show_alert=True)
        elif sub_act == "bio":
            new_val = 0 if self_data.get("clock_bio") else 1
            await db.update_self_bot(owner_id, clock_bio=new_val)
            await callback.answer(f"📝 ساعت بیو {'🟢 روشن' if new_val else '🔴 خاموش'} شد.", show_alert=True)
        elif sub_act == "afk":
            cur_afk = self_data.get("afk_status", 0)
            new_afk = 0 if cur_afk == 1 else 1
            await db.update_self_bot(owner_id, afk_status=new_afk)
            await callback.answer(f"💤 منشی AFK {'🟢 فعال' if new_afk else '🔴 غیرفعال'} شد.", show_alert=True)

    elif action == "cycle":
        self_data = await db.get_self_bot(owner_id)
        fonts = [f[0] for f in FONT_LIST]
        cur = self_data.get("clock_font", "persian")
        next_font = fonts[(fonts.index(cur) + 1) % len(fonts)] if cur in fonts else "persian"
        await db.update_self_bot(owner_id, clock_font=next_font)
        await callback.answer(f"🎨 فونت ساعت به «{next_font}» تغییر یافت.", show_alert=True)

    elif action == "react":
        react_type = parts[2]
        emoji_map = {
            "heart": "❤️", "fire": "🔥", "like": "👍", 
            "clap": "👏", "laugh": "😂", "bolt": "⚡️", "off": "off"
        }
        selected = emoji_map.get(react_type, "off")
        await db.set_setting(f"self_react_{owner_id}", selected)
        await callback.answer(f"🔔 ری‌اکشن به {selected} تنظیم شد.", show_alert=True)

    elif action == "stopspam":
        from self_manager import stop_user_spam
        stop_user_spam(owner_id)
        await callback.answer("🛑 تمامی ارسال‌های مکرر و اسپمر متوقف شدند.", show_alert=True)

    elif action == "ping":
        await callback.answer("🏓 Pong! سلف‌بات آنلاین و متصل است.", show_alert=True)
EOF

cat << 'EOF' > self_manager.py
import asyncio
import datetime
import logging
import os
import random
import re
import json
import ssl
import hashlib
import urllib.parse
import urllib.request
from zoneinfo import ZoneInfo
from telethon import TelegramClient, events, functions, types
from telethon.sessions import StringSession
from telethon.errors import AuthKeyUnregisteredError, SessionRevokedError, UserDeactivatedError

from config import config
import database as db

logging.basicConfig(level=logging.INFO)

ACTIVE_CLIENTS = {}
SPAM_STOP_FLAGS = {}
PV_MESSAGE_CACHE = {}

TIME_FONTS = {
    "persian": {"0": "۰", "1": "۱", "2": "۲", "3": "۳", "4": "۴", "5": "۵", "6": "۶", "7": "۷", "8": "۸", "9": "۹", ":": ":"},
    "digital": {"0": "𝟘", "1": "𝟙", "2": "𝟚", "3": "𝟛", "4": "𝟜", "5": "𝟝", "6": "𝟞", "7": "𝟟", "8": "𝟠", "9": "𝟡", ":": ":"},
    "superscript": {"0": "⁰", "1": "¹", "2": "²", "3": "³", "4": "⁴", "5": "⁵", "6": "⁶", "7": "⁷", "8": "⁸", "9": "⁹", ":": ":"},
    "subscript": {"0": "₀", "1": "₁", "2": "₂", "3": "₃", "4": "₄", "5": "₅", "6": "₆", "7": "₇", "8": "₈", "9": "₉", ":": ":"},
    "bold": {"0": "𝟎", "1": "𝟏", "2": "𝟐", "3": "𝟑", "4": "𝟒", "5": "𝟓", "6": "𝟔", "7": "𝟕", "8": "𝟖", "9": "𝟗", ":": ":"},
    "sans_bold": {"0": "𝟬", "1": "𝟭", "2": "𝟮", "3": "𝟯", "4": "𝟰", "5": "𝟱", "6": "𝟲", "7": "𝟳", "8": "𝟴", "9": "𝟵", ":": ":"},
    "sans": {"0": "𝟢", "1": "𝟣", "2": "𝟤", "3": "𝟥", "4": "𝟦", "5": "𝟧", "6": "𝟨", "7": "𝟩", "8": "𝟪", "9": "𝟫", ":": ":"},
    "monospace": {"0": "𝟶", "1": "𝟷", "2": "𝟸", "3": "𝟹", "4": "𝟺", "5": "𝟻", "6": "𝟼", "7": "𝟽", "8": "𝟾", "9": "𝟿", ":": ":"},
    "circled_dark": {"0": "⓿", "1": "➊", "2": "➋", "3": "➌", "4": "➍", "5": "➎", "6": "➏", "7": "➐", "8": "➑", "9": "➒", ":": ":"},
    "circled_white": {"0": "⓪", "1": "①", "2": "②", "3": "③", "4": "④", "5": "⑤", "6": "⑥", "7": "⑦", "8": "⑧", "9": "⑨", ":": ":"},
    "circled_serif": {"0": "🄋", "1": "➀", "2": "➁", "3": "➂", "4": "➃", "5": "➄", "6": "➅", "7": "➆", "8": "➇", "9": "➈", ":": ":"},
    "parenthesized": {"0": "⒪", "1": "⑴", "2": "⑵", "3": "⑶", "4": "⑷", "5": "⑸", "6": "⑹", "7": "⑺", "8": "⑻", "9": "⑼", ":": ":"},
    "dotted": {"0": "🄀", "1": "⒈", "2": "⒉", "3": "⒋", "4": "⒋", "5": "⒌", "6": "⒍", "7": "⒎", "8": "⒏", "9": "⒐", ":": ":"},
    "square_dark": {"0": "🄌", "1": "➊", "2": "➋", "3": "➌", "4": "➍", "5": "➎", "6": "➏", "7": "➐", "8": "➑", "9": "➒", ":": ":"},
    "fullwidth": {"0": "０", "1": "１", "2": "２", "3": "３", "4": "４", "5": "５", "6": "６", "7": "７", "8": "８", "9": "９", ":": "："},
    "segment_7": {"0": "🯰", "1": "🯱", "2": "🯲", "3": "𝯳", "4": "𝯴", "5": "𝯵", "6": "𝯶", "7": "𝯷", "8": "𝯸", "9": "𝯹", ":": ":"},
    "arabic": {"0": "٠", "1": "١", "2": "٢", "3": "٣", "4": "٤", "5": "٥", "6": "٦", "7": "٧", "8": "٨", "9": "٩", ":": ":"},
    "roman": {"0": "0", "1": "Ⅰ", "2": "Ⅱ", "3": "Ⅲ", "4": "Ⅳ", "5": "Ⅴ", "6": "Ⅵ", "7": "Ⅶ", "8": "Ⅷ", "9": "Ⅸ", ":": ":"},
    "bubble_black": {"0": "⓿", "1": "❶", "2": "❷", "3": "❸", "4": "❹", "5": "❺", "6": "❻", "7": "❼", "8": "❽", "9": "❾", ":": ":"},
    "braille": {"0": "⠚", "1": "⠁", "2": "⠃", "3": "⠉", "4": "⠙", "5": "⠑", "6": "⠋", "7": "⠛", "8": "⠓", "9": "⠊", ":": "⠒"},
    "devanagari": {"0": "०", "1": "१", "2": "२", "3": "३", "4": "४", "5": "५", "6": "६", "7": "७", "8": "८", "9": "९", ":": ":"},
    "bengali": {"0": "০", "1": "১", "2": "২", "3": "৩", "4": "৪", "5": "৫", "6": "৬", "7": "৭", "8": "৮", "9": "৯", ":": ":"},
    "thai": {"0": "๐", "1": "๑", "2": "๒", "3": "๓", "4": "๔", "5": "๕", "6": "๖", "7": "๗", "8": "๘", "9": "๙", ":": ":"},
    "tibetan": {"0": "༠", "1": "༡", "2": "༢", "3": "༣", "4": "༤", "5": "༥", "6": "༦", "7": "༧", "8": "༨", "9": "༩", ":": ":"},
    "khmer": {"0": "០", "1": "១", "2": "۲", "3": "۳", "4": "۴", "5": "۵", "6": "۶", "7": "۷", "8": "۸", "9": "۹", ":": ":"},
    "myanmar": {"0": "၀", "1": "၁", "2": "၂", "3": "၃", "4": "၄", "5": "၅", "6": "၆", "7": "၇", "8": "၈", "9": "၉", ":": ":"},
    "gurmukhi": {"0": "੦", "1": "੧", "2": "੨", "3": "੩", "4": "੪", "5": "੫", "6": "੬", "7": "੭", "8": "੮", "9": "੯", ":": ":"},
    "kannada": {"0": "೦", "1": "೧", "2": "೨", "3": "೩", "4": "೪", "5": "೫", "6": "೬", "7": "೭", "8": "೮", "9": "೯", ":": ":"},
    "malayalam": {"0": "൦", "1": "൧", "2": "൨", "3": "൩", "4": "൪", "5": "൫", "6": "൬", "7": "൭", "8": "൮", "9": "൯", ":": ":"},
    "tamil": {"0": "௦", "1": "௧", "2": "௨", "3": "௩", "4": "௪", "5": "௫", "6": "௬", "7": "௭", "8": "௮", "9": "௯", ":": ":"},
    "telugu": {"0": "౦", "1": "౧", "2": "౨", "3": "౩", "4": "౪", "5": "౫", "6": "౬", "7": "౭", "8": "౮", "9": "౯", ":": ":"},
    "gujarati": {"0": "૦", "1": "૧", "2": "૨", "3": "૩", "4": "૪", "5": "૫", "6": "૬", "7": "૭", "8": "૮", "9": "૯", ":": ":"},
    "oriya": {"0": "୦", "1": "୧", "2": "୨", "3": "୩", "4": "୪", "5": "୫", "6": "୬", "7": "୭", "8": "୮", "9": "୯", ":": ":"},
    "sinhala": {"0": "෦", "1": "෧", "2": "෨", "3": "෩", "4": "෪", "5": "෫", "6": "෬", "7": "෭", "8": "෮", "9": "෯", ":": ":"},
    "lao": {"0": "໐", "1": "໑", "2": "໒", "3": "໓", "4": "໔", "5": "໕", "6": "໖", "7": "໗", "8": "໘", "9": "໙", ":": ":"},
    "regular": {"0": "0", "1": "1", "2": "2", "3": "3", "4": "4", "5": "5", "6": "6", "7": "7", "8": "8", "9": "9", ":": ":"}
}

def stop_user_spam(user_id: int):
    SPAM_STOP_FLAGS[user_id] = True

def get_styled_time(font_name="persian"):
    now = datetime.datetime.now(ZoneInfo("Asia/Tehran"))
    raw = now.strftime("%H:%M")
    fmap = TIME_FONTS.get(font_name, TIME_FONTS["persian"])
    return "".join(fmap.get(c, c) for c in raw)

def parse_telegram_message_link(link: str):
    if not link:
        return None, None
    link = link.strip()
    m_priv = re.search(r"t\.me/c/(\d+)/(\d+)", link)
    if m_priv:
        channel_id = int(f"-100{m_priv.group(1)}")
        msg_id = int(m_priv.group(2))
        return channel_id, msg_id

    m_pub = re.search(r"t\.me/([a-zA-Z0-9_]+)/(\d+)", link)
    if m_pub:
        username = m_pub.group(1)
        msg_id = int(m_pub.group(2))
        return username, msg_id

    return None, None

async def search_music_online(query: str):
    try:
        ctx = ssl.create_default_context()
        ctx.check_hostname = False
        ctx.verify_mode = ssl.CERT_NONE
        url = f"https://itunes.apple.com/search?term={urllib.parse.quote(query)}&entity=song&limit=5"
        req = urllib.request.Request(url, headers={"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"})
        def fetch():
            with urllib.request.urlopen(req, context=ctx, timeout=8) as r:
                return json.loads(r.read().decode())
        data = await asyncio.to_thread(fetch)
        results = data.get("results", [])
        tracks = []
        for item in results:
            tracks.append({
                "title": item.get("trackName", "آهنگ"),
                "artist": {"name": item.get("artistName", "خواننده")},
                "preview": item.get("previewUrl", "")
            })
        return tracks
    except Exception as e:
        logging.error(f"Music search error: {e}")
        return []

async def resolve_destination_peer(client, dest_str: str):
    if not dest_str or dest_str.strip().lower() == "me":
        return "me"
    target = dest_str.strip()
    try:
        if target.startswith("-100") or (target.startswith("-") and target[1:].isdigit()):
            return int(target)
        if target.isdigit():
            return int(target)
        if target.startswith("@"):
            return target
        return target
    except Exception:
        return "me"

async def start_userbot_client(user_id: int, session_str: str):
    if user_id in ACTIVE_CLIENTS:
        try:
            await ACTIVE_CLIENTS[user_id].disconnect()
        except Exception:
            pass

    client = TelegramClient(
        StringSession(session_str),
        api_id=config.API_ID,
        api_hash=config.API_HASH,
        device_model="PuLaSeLf Core",
        app_version="PuLaSeLf v2.0",
        system_version="PuLa-OS",
        lang_code="fa"
    )

    try:
        await client.connect()
        if not await client.is_user_authorized():
            await db.delete_self_bot(user_id)
            return False

        ACTIVE_CLIENTS[user_id] = client
        PV_MESSAGE_CACHE[user_id] = {}
        SPAM_STOP_FLAGS[user_id] = False

        logging.info(f"🚀 PuLaSeLf userbot active for {user_id}")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(پنل|\.panel|panel)$"))
        async def panel_trigger(event):
            sent = False
            try:
                input_chat = await event.get_input_chat()
            except Exception:
                input_chat = event.chat_id

            for attempt in range(4):
                try:
                    results = await client.inline_query("PuLaherperbot", f"panel_{user_id}")
                    if results:
                        await results[0].click(input_chat, reply_to=event.reply_to_msg_id)
                        await event.delete()
                        sent = True
                        break
                except Exception as e:
                    logging.warning(f"Inline retry attempt {attempt + 1}: {e}")
                await asyncio.sleep(0.25)

            if not sent:
                me = await client.get_me()
                self_data = await db.get_self_bot(user_id)
                tmpl = self_data.get("name_template") or "User {clock}"
                c_name = "🟢 روشن" if self_data and self_data.get('clock_name') else "🔴 خاموش"
                c_bio = "🟢 روشن" if self_data and self_data.get('clock_bio') else "🔴 خاموش"
                
                panel_text = (
                    "👑 <b>کنترل پنل کاربری سلف‌بات پیشرفته PuLaSeLf</b>\n"
                    "━━━━━━━━━━━━━━━━━━━━\n\n"
                    f"👤 <b>کاربر:</b> {me.first_name}\n"
                    f"🆔 <b>شناسه عددی:</b> <code>{user_id}</code>\n"
                    f"🕒 <b>ساعت روی اسم:</b> {c_name}\n"
                    f"📝 <b>ساعت روی بیو:</b> {c_bio}\n"
                    f"🏷 <b>الگوی اسم:</b> <code>{tmpl}</code>\n\n"
                    "━━━━━━━━━━━━━━━━━━━━\n"
                    "⚙️ <b>دستورات پرکاربرد سلف:</b>\n\n"
                    "📱 <b>اسپمر:</b>\n"
                    "• <code>اسپم 50 بار هر 2 ثانیه سلام</code>\n"
                    "• <code>توقف اسپم</code> | <code>.stopspam</code>\n\n"
                    "✏️ <b>ادیت و حذف پیام:</b>\n"
                    "• <code>حذف</code> | <code>.del</code> (روی ریپلای)\n"
                    "• <code>ادیت [متن]</code> | <code>.edit [text]</code> (روی ریپلای)\n\n"
                    "💾 <b>ذخیره و فوروارد (ضد قفل):</b>\n"
                    "• <code>سیو</code> | <code>.save</code> (ریپلای یا با لینک)\n"
                    "• <code>فور</code> | <code>.for</code> (ریپلای یا با لینک)\n\n"
                    "🛠️ <b>ابزارها:</b>\n"
                    "• <code>پینگ</code> | <code>ساعت</code> | <code>تگ همه</code> | <code>موزیک</code> | <code>اطلاعات</code>\n\n"
                    "📖 برای دیدن لیست تمام دستورات ارسال کنید: <code>راهنما</code> یا <code>.help</code>"
                )
                try:
                    await event.edit(panel_text, parse_mode="html")
                except Exception:
                    pass

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:\.help|راهنما|دستورات|\.menu|منو|help)$"))
        async def help_cmd(event):
            help_text = (
                "👑 <b>راهنمای جامع دستورات سلف‌بات PuLaSeLf:</b>\n"
                "━━━━━━━━━━━━━━━━━━━━\n\n"
                "📱 <b>اسپمر و ارسال مکرر (فارسی و انگلیسی):</b>\n"
                "• <code>اسپم 100 بار هر 2 ثانیه متن</code>\n"
                "• <code>اسپم 20 بار هر 1 دقیقه متن</code>\n"
                "• <code>.spam 20 1.5 hello</code> | <code>spam 20 1.5 hello</code>\n"
                "• <code>توقف اسپم</code> | <code>توقف</code> | <code>.stopspam</code> | <code>stop</code>\n\n"
                "✏️ <b>مدیریت و ادیت پیام‌ها:</b>\n"
                "• <code>حذف</code> | <code>.del</code> ➔ پاک کردن پیام ریپلای‌شده\n"
                "• <code>ادیت [متن جدید]</code> | <code>.edit [text]</code> ➔ تغییر پیام شما\n\n"
                "💾 <b>ذخیره‌ساز و ضد کپی (حتی کانال‌های قفل):</b>\n"
                "• <code>سیو</code> | <code>.save</code> (ریپلای روی پیام یا <code>سیو لینک</code>)\n"
                "• <code>فور</code> | <code>.for</code> (ریپلای روی پیام یا <code>فور لینک</code>)\n"
                "• <code>سیو تایمر روشن/خاموش</code> | <code>.savettl on/off</code>\n"
                "• <code>انتی دلیت روشن/خاموش</code> | <code>.antidel on/off</code>\n"
                "• <code>انتی ادیت روشن/خاموش</code> | <code>.antiedit on/off</code>\n"
                "• <code>سیو پیوی روشن/خاموش</code> | <code>.savepv on/off</code>\n\n"
                "👤 <b>پروفایل و ساعت زنده:</b>\n"
                "• <code>تنظیم اسم Ali {clock}</code> | <code>.setname Ali {clock}</code>\n"
                "• <code>ساعت اسم روشن/خاموش</code> | <code>.name on/off</code>\n"
                "• <code>ساعت بیو روشن/خاموش</code> | <code>.bio on/off</code>\n"
                "• <code>حذف ساعت اسم</code> | <code>.resetname</code>\n"
                "• <code>فونت [نام فونت]</code> | <code>.font [name]</code> (۳۶ فونت عددی)\n\n"
                "✍️ <b>استایل و فرمت خودکار:</b>\n"
                "• <code>بولد روشن/خاموش</code> | <code>.autobold on/off</code>\n"
                "• <code>ایتالیک روشن/خاموش</code> | <code>.autoitalic on/off</code>\n"
                "• <code>مونو روشن/خاموش</code> | <code>.automono on/off</code>\n\n"
                "💬 <b>پاسخگوی خودکار کلمات (منشی):</b>\n"
                "• <code>پاسخ اضافه کلمه | جواب</code> | <code>.autoans add کلمه | جواب</code>\n"
                "• <code>پاسخ حذف کلمه</code> | <code>.autoans del کلمه</code>\n"
                "• <code>پاسخ لیست</code> | <code>.autoans list</code>\n\n"
                "🛠️ <b>ابزارهای کاربردی:</b>\n"
                "• <code>پینگ</code> | <code>.ping</code> ➔ تست سرعت سلف\n"
                "• <code>ساعت</code> | <code>تاریخ</code> | <code>.time</code> ➔ زمان دقیق تهران\n"
                "• <code>حساب 2+2*5</code> | <code>.calc 2+2*5</code> ➔ ماشین‌حساب ریاضی\n"
                "• <code>موزیک [نام آهنگ]</code> | <code>.music [name]</code> ➔ دانلود آنلاین آهنگ\n"
                "• <code>اطلاعات</code> | <code>.info</code> (ریپلای/آیدی/یوزر) ➔ مشخصات و عکس کاربر\n"
                "• <code>تگ همه [متن]</code> | <code>.tagall [text]</code> ➔ منشن کردن اعضای گروه\n"
                "• <code>آمار چت</code> | <code>.lm</code> ➔ آنالیز ۱۰۰ پیام اخیر\n"
                "• <code>اف کی [دلیل]</code> | <code>.afk [reason]</code> ➔ منشی استراحت\n"
                "• <code>خروج اف کی</code> | <code>.unafk</code> ➔ خروج از استراحت\n\n"
                "━━━━━━━━━━━━━━━━━━━━\n"
                "⚡️ <b>PuLaSeLf Engine v2.5</b>"
            )
            await event.edit(help_text, parse_mode="html")

        # --- ۱. اسپمر دو زبانه و هوشمند ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:اسپم|\.spam|spam)\s+(\d+)\s*(?:بار)?\s*(?:هر)?\s*([\d\.]+)?\s*(ثانیه|دقیقه|ساعت|روز|s|m|h|d)?(?:\s+(.+))?$"))
        async def advanced_spam_cmd(event):
            count = int(event.pattern_match.group(1))
            interval_raw = event.pattern_match.group(2)
            unit_raw = event.pattern_match.group(3)
            text_to_send = event.pattern_match.group(4)

            interval = float(interval_raw) if interval_raw else 1.0
            if unit_raw in ["دقیقه", "m"]:
                interval *= 60
            elif unit_raw in ["ساعت", "h"]:
                interval *= 3600
            elif unit_raw in ["روز", "d"]:
                interval *= 86400

            if not text_to_send:
                text_to_send = "Spam"

            chat_id = event.chat_id
            SPAM_STOP_FLAGS[user_id] = False
            await event.delete()

            for _ in range(count):
                if SPAM_STOP_FLAGS.get(user_id, False):
                    break
                await client.send_message(chat_id, text_to_send)
                await asyncio.sleep(interval)

            SPAM_STOP_FLAGS[user_id] = False

        # --- ۲. توقف قطعی و دو زبانه اسپمر ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:توقف\s*اسپم|توقف|\.stopspam|\.stop|stopspam|stop|استپ)$"))
        async def stop_all_spam_cmd(event):
            SPAM_STOP_FLAGS[user_id] = True
            await event.edit("🛑 <b>تمامی ارسال‌های مکرر و اسپمر متوقف شدند.</b>", parse_mode="html")

        # --- ۳. حذف پیام دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:حذف|دلیت|\.del|del|\.delete|delete)$"))
        async def delete_reply_msg_handler(event):
            if not event.is_reply:
                await event.edit("⚠️ <i>لطفاً این دستور را روی یک پیام ریپلای کنید.</i>", parse_mode="html")
                await asyncio.sleep(2)
                await event.delete()
                return

            rep = await event.get_reply_message()
            await event.delete()
            try:
                await rep.delete()
            except Exception:
                pass

        # --- ۴. ادیت پیام دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:ادیت|ویرایش|\.edit|edit)\s+(.+)$"))
        async def edit_reply_msg_handler(event):
            new_text = event.pattern_match.group(1).strip()
            if not event.is_reply:
                await event.edit("⚠️ <i>لطفاً روی پیام خودتان ریپلای کنید:</i> <code>ادیت متن جدید</code>", parse_mode="html")
                await asyncio.sleep(2.5)
                await event.delete()
                return

            rep = await event.get_reply_message()
            if not rep.out:
                await event.edit("⚠️ <i>فقط می‌توانید پیام‌های ارسالی خودتان را ادیت کنید.</i>", parse_mode="html")
                await asyncio.sleep(2.5)
                await event.delete()
                return

            try:
                await rep.edit(new_text)
                await event.delete()
            except Exception as e:
                await event.edit(f"❌ خطا در ادیت پیام: {e}")

        # --- ۵. سیو پیام دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:سیو|\.save|save|\.dl|dl)(?:\s+(.+))?$"))
        async def save_msg_handler(event):
            link_input = event.pattern_match.group(1)
            target_msg = None

            if link_input:
                peer, msg_id = parse_telegram_message_link(link_input)
                if not peer or not msg_id:
                    await event.edit("⚠️ لینک تلگرام نامعتبر است.\nمثال: <code>سیو https://t.me/channel/123</code>", parse_mode="html")
                    return
                await event.edit(f"⏳ در حال دریافت پیام از لینک...", parse_mode="html")
                try:
                    target_msg = await client.get_messages(peer, ids=msg_id)
                except Exception as e:
                    await event.edit(f"❌ خطا در دسترسی به پیام لینک: {e}")
                    return
            elif event.is_reply:
                target_msg = await event.get_reply_message()
                await event.edit("⏳ در حال دریافت و کپی پیام در Saved Messages...")
            else:
                await event.edit("⚠️ لطفاً روی پیام ریپلای کنید یا لینک پیام را بفرستید:\nمثال: <code>سیو https://t.me/channel/123</code>", parse_mode="html")
                return

            if not target_msg:
                await event.edit("❌ پیام مورد نظر یافت نشد.")
                return

            try:
                await client.forward_messages("me", target_msg)
                await event.edit("✅ <b>پیام با موفقیت در Saved Messages ذخیره شد!</b>", parse_mode="html")
            except Exception:
                try:
                    if target_msg.media:
                        file_path = await client.download_media(target_msg)
                        if file_path:
                            cap = target_msg.text or target_msg.caption or "💾 رسانه کپی‌شده:"
                            await client.send_file("me", file_path, caption=cap)
                            if os.path.exists(file_path):
                                os.remove(file_path)
                            await event.edit("✅ <b>رسانه از چت قفل در Saved Messages کپی شد!</b>", parse_mode="html")
                        else:
                            await event.edit("❌ خطا در دانلود رسانه.")
                    elif target_msg.text:
                        await client.send_message("me", target_msg.text)
                        await event.edit("✅ <b>متن پیام با موفقیت در Saved Messages کپی شد!</b>", parse_mode="html")
                    else:
                        await event.edit("❌ محتوای قابل ذخیره‌ای یافت نشد.")
                except Exception as ex:
                    await event.edit(f"❌ خطا در ذخیره پیام: {ex}")

        # --- ۶. فور پیام دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:فور|\.for|for|\.f|share)(?:\s+(.+))?$"))
        async def forward_share_link_handler(event):
            link_input = event.pattern_match.group(1)
            target_msg = None

            if link_input:
                peer, msg_id = parse_telegram_message_link(link_input)
                if not peer or not msg_id:
                    await event.edit("⚠️ لینک تلگرام نامعتبر است.\nمثال: <code>فور https://t.me/channel/123</code>", parse_mode="html")
                    return
                await event.edit(f"⏳ در حال استخراج محتوای لینک...", parse_mode="html")
                try:
                    target_msg = await client.get_messages(peer, ids=msg_id)
                except Exception as e:
                    await event.edit(f"❌ خطا در دسترسی به پیام: {e}")
                    return
            elif event.is_reply:
                target_msg = await event.get_reply_message()
            else:
                await event.edit("⚠️ لطفاً روی پیام ریپلای کنید یا لینک پیام را بفرستید:\nمثال: <code>فور https://t.me/channel/123</code>", parse_mode="html")
                return

            if not target_msg:
                await event.edit("❌ پیام مورد نظر یافت نشد.")
                return

            content_text = target_msg.text or target_msg.caption or ""
            encoded_text = urllib.parse.quote(content_text.strip())
            share_link = f"https://t.me/share/url?url=&text={encoded_text}" if encoded_text else "https://t.me/share/url"

            response_html = (
                "📤 <b>فوروارد و ارسال سریع پیام:</b>\n"
                "━━━━━━━━━━━━━━━━━━━━\n\n"
                f"🔗 <a href='{share_link}'>👈 <b>برای انتخاب چت و فوروارد اینجا کلیک کنید</b> 👉</a>\n\n"
                "💡 <i>با زدن روی لینک بالا، صفحه انتخاب چت باز می‌شود تا پیام را به هر گروه یا پیوی بفرستید.</i>"
            )
            await event.edit(response_html, parse_mode="html")

        # --- ۷. تگ همگانی دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:تگ\s*همه|تگ|\.tagall|\.tag|tagall|tag)(?:\s+(.+))?$"))
        async def tagall_cmd(event):
            if not event.is_group:
                await event.edit("⚠️ <i>این دستور فقط داخل گروه‌ها کار می‌کند.</i>", parse_mode="html")
                return
            msg_custom = event.pattern_match.group(1) or "سلام دوستان"
            await event.delete()
            async for user in client.iter_participants(event.chat_id):
                if not user.bot and not user.deleted:
                    mention = f"[{user.first_name}](tg://user?id={user.id})"
                    await client.send_message(event.chat_id, f"{mention} {msg_custom}")
                    await asyncio.sleep(1.5)

        # --- ۸. مشخصات کاربر دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:اطلاعات|مشخصات|\.info|info|\.user|user)(?:\s+(.+))?$"))
        async def info_cmd(event):
            raw_arg = event.pattern_match.group(1)
            reply_target_id = event.reply_to_msg_id if event.is_reply else None
            chat_id = event.chat_id
            target_entity = None

            await event.delete()

            if raw_arg:
                arg = raw_arg.strip()
                try:
                    if arg.isdigit() or (arg.startswith("-") and arg[1:].isdigit()):
                        target_entity = await client.get_entity(int(arg))
                    else:
                        clean_un = arg.replace("https://t.me/", "").replace("@", "").strip()
                        target_entity = await client.get_entity(clean_un)
                except Exception:
                    await client.send_message(chat_id, f"❌ کاربر با شناسه/یوزرنیم «{arg}» یافت نشد.", reply_to=reply_target_id)
                    return
            elif event.is_reply:
                rep = await event.get_reply_message()
                try:
                    target_entity = await client.get_entity(rep.sender_id)
                except Exception:
                    pass
            elif event.is_private:
                target_entity = await client.get_chat()
            else:
                target_entity = await client.get_me()

            if not target_entity:
                return

            t_id = target_entity.id
            fn = getattr(target_entity, 'first_name', '') or ''
            ln = getattr(target_entity, 'last_name', '') or ''
            full_name = f"{fn} {ln}".strip() or "بدون نام"
            un = f"@{target_entity.username}" if getattr(target_entity, 'username', None) else "ندارد"
            bot_stat = "بله 🤖" if getattr(target_entity, 'bot', False) else "خیر 👤"
            prem_stat = "دارد ⭐️" if getattr(target_entity, 'premium', False) else "ندارد ❌"

            bio = "ندارد"
            try:
                full_user = await client(functions.users.GetFullUserRequest(id=t_id))
                bio = full_user.full_user.about or "ندارد"
            except Exception:
                pass

            total_msgs = 0
            today_msgs = 0
            try:
                total_res = await client.get_messages(chat_id, from_user=t_id, limit=0)
                total_msgs = getattr(total_res, 'total', 0)

                now_tehran = datetime.datetime.now(ZoneInfo("Asia/Tehran"))
                start_of_today = now_tehran.replace(hour=0, minute=0, second=0, microsecond=0)
                async for msg in client.iter_messages(chat_id, from_user=t_id):
                    msg_tehran = msg.date.astimezone(ZoneInfo("Asia/Tehran"))
                    if msg_tehran >= start_of_today:
                        today_msgs += 1
                    else:
                        break
            except Exception:
                pass

            caption = (
                "👤 <b>پروفایل و مشخصات کاربر:</b>\n"
                "━━━━━━━━━━━━━━━━━━━━\n\n"
                f"📝 <b>نام:</b> {full_name}\n"
                f"🆔 <b>آیدی عددی:</b> <code>{t_id}</code>\n"
                f"🏷 <b>یوزرنیم:</b> {un}\n"
                f"🤖 <b>ربات:</b> {bot_stat}\n"
                f"💎 <b>پرمیوم:</b> {prem_stat}\n\n"
                f"💬 <b>پیام‌های امروز در این چت:</b> <code>{today_msgs}</code>\n"
                f"📊 <b>کل پیام‌ها در این چت:</b> <code>{total_msgs}</code>\n\n"
                f"📄 <b>بیوگرافی:</b>\n<i>{bio}</i>\n"
                "━━━━━━━━━━━━━━━━━━━━"
            )

            photo_path = None
            try:
                photo_path = await client.download_profile_photo(target_entity, file=f"pfp_{t_id}_{user_id}.jpg")
            except Exception:
                photo_path = None

            if photo_path and os.path.exists(photo_path):
                try:
                    await client.send_file(chat_id, photo_path, caption=caption, parse_mode="html", reply_to=reply_target_id)
                finally:
                    if os.path.exists(photo_path):
                        os.remove(photo_path)
            else:
                await client.send_message(chat_id, caption, parse_mode="html", reply_to=reply_target_id)

        # --- ۹. آنالیز چت (LM) دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:آمار\s*چت|امار\s*چت|\.lm|lm|\.stats|stats)$"))
        async def lm_cmd(event):
            await event.edit("⏳ <i>در حال تحلیل ۱۰۰ پیام اخیر چت...</i>", parse_mode="html")
            total, media_cnt, text_cnt = 0, 0, 0
            async for msg in client.iter_messages(event.chat_id, limit=100):
                total += 1
                if msg.media:
                    media_cnt += 1
                else:
                    text_cnt += 1
            await event.edit(
                "📊 <b>گزارش آماری ۱۰۰ پیام اخیر چت:</b>\n"
                "━━━━━━━━━━━━━━━━━━━━\n\n"
                f"💬 <b>کل پیام‌ها:</b> <code>{total}</code>\n"
                f"📝 <b>پیام‌های متنی:</b> <code>{text_cnt}</code>\n"
                f"📁 <b>فایل و رسانه:</b> <code>{media_cnt}</code>\n"
                "━━━━━━━━━━━━━━━━━━━━",
                parse_mode="html"
            )

        # --- ۱۰. پاسخگوی خودکار کلمات دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:پاسخ|\.autoans|autoans)\s+(اضافه|حذف|لیست|add|del|list)(?:\s+(.+))?$"))
        async def autoans_manage(event):
            act_raw = event.pattern_match.group(1).lower()
            arg = event.pattern_match.group(2)
            raw = await db.get_setting(f"autoans_{user_id}", "{}")
            try:
                ans_dict = json.loads(raw)
            except Exception:
                ans_dict = {}

            if act_raw in ["add", "اضافه"]:
                if not arg or "|" not in arg:
                    await event.edit("⚠️ <b>الگو:</b> <code>پاسخ اضافه کلمه | جواب</code>", parse_mode="html")
                    return
                k, v = arg.split("|", 1)
                ans_dict[k.strip().lower()] = v.strip()
                await db.set_setting(f"autoans_{user_id}", json.dumps(ans_dict, ensure_ascii=False))
                await event.edit(f"✅ پاسخ خودکار برای «<code>{k.strip()}</code>» تنظیم شد.", parse_mode="html")
            elif act_raw in ["del", "حذف"]:
                if not arg or arg.strip().lower() not in ans_dict:
                    await event.edit("❌ <i>کلمه در لیست یافت نشد.</i>", parse_mode="html")
                    return
                del ans_dict[arg.strip().lower()]
                await db.set_setting(f"autoans_{user_id}", json.dumps(ans_dict, ensure_ascii=False))
                await event.edit(f"🗑 کلمه «<code>{arg.strip()}</code>» با موفقیت حذف شد.", parse_mode="html")
            elif act_raw in ["list", "لیست"]:
                if not ans_dict:
                    await event.edit("📭 <i>هیچ پاسخ خودکاری ثبت نشده است.</i>", parse_mode="html")
                    return
                t = "💬 <b>لیست پاسخ‌های خودکار تنظیم‌شده:</b>\n━━━━━━━━━━━━━━━━━━━━\n\n"
                for idx, (k, v) in enumerate(ans_dict.items(), 1):
                    t += f"{idx}. <code>{k}</code> ➔ <i>{v}</i>\n"
                t += "━━━━━━━━━━━━━━━━━━━━"
                await event.edit(t, parse_mode="html")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^\.setdst\s+(ttl|del|edit|pv)\s+(.+)$"))
        async def set_destination_cmd(event):
            target_type = event.pattern_match.group(1).lower()
            dest_input = event.pattern_match.group(2).strip()

            target_names = {
                "ttl": ("dst_ttl", "عکس‌های تایمردار"),
                "del": ("dst_del", "پیام‌های حذف‌شده (آنتی‌دلیت)"),
                "edit": ("dst_edit", "پیام‌های ویرایش‌شده (آنتی‌ادیت)"),
                "pv": ("dst_pv", "سیو پیام‌های پیوی")
            }
            col_name, fa_title = target_names[target_type]
            await db.update_self_bot(user_id, **{col_name: dest_input})

            dest_display = "Saved Messages (پیام‌های ذخیره‌شده)" if dest_input.lower() == "me" else f"کانال خصوصی <code>{dest_input}</code>"
            await event.edit(f"✅ مقصد ارسال **{fa_title}** با موفقیت به {dest_display} تغییر یافت.", parse_mode="html")

        @client.on(events.NewMessage(incoming=True))
        async def incoming_global_listener(event):
            react_emoji = await db.get_setting(f"self_react_{user_id}", "off")
            if react_emoji and react_emoji != "off":
                try:
                    await client(functions.messages.SendReactionRequest(
                        peer=event.chat_id,
                        msg_id=event.id,
                        reaction=[types.ReactionEmoji(emoticon=react_emoji)]
                    ))
                except Exception:
                    pass

            if event.text:
                raw_ans = await db.get_setting(f"autoans_{user_id}", "{}")
                try:
                    ans_map = json.loads(raw_ans)
                    k_match = event.text.strip().lower()
                    if k_match in ans_map:
                        await event.reply(ans_map[k_match])
                except Exception:
                    pass

            if event.is_private or event.mentioned:
                self_data = await db.get_self_bot(user_id)
                if self_data and self_data.get("afk_status", 0) == 1:
                    r = self_data.get("afk_reason") or "در دسترس نیستم"
                    await event.reply(f"💤 **کاربر در حال حاضر آفلاین (AFK) است.**\n📝 دلیل: _{r}_")

        @client.on(events.NewMessage(incoming=True, func=lambda e: e.is_private))
        async def pv_message_listener(event):
            self_data = await db.get_self_bot(user_id)
            if not self_data:
                return

            sender = await event.get_sender()
            sender_name = getattr(sender, "first_name", "کاربر")
            time_now = datetime.datetime.now(ZoneInfo("Asia/Tehran")).strftime("%H:%M:%S")

            user_cache = PV_MESSAGE_CACHE.setdefault(user_id, {})
            user_cache[event.id] = {
                "sender_id": event.sender_id,
                "sender_name": sender_name,
                "text": event.text or "بدون متن",
                "date": time_now,
                "media": event.media
            }
            if len(user_cache) > 500:
                user_cache.pop(next(iter(user_cache)))

            if self_data.get("save_ttl", 1) == 1 and event.media:
                is_ttl = getattr(event.media, "ttl_seconds", None) or getattr(event.message, "ttl_period", None)
                if is_ttl:
                    try:
                        dst_peer = await resolve_destination_peer(client, self_data.get("dst_ttl", "me"))
                        path = await event.download_media()
                        if path:
                            cap = f"🔒 <b>عکس/رسانه تایمردار دریافتی از {sender_name}:</b>\n⏱ تایمر: <code>{is_ttl}</code> ثانیه\n🕒 زمان: <code>{time_now}</code>"
                            try:
                                await client.send_file(dst_peer, path, caption=cap, parse_mode="html")
                            except Exception:
                                await client.send_file("me", path, caption=cap, parse_mode="html")
                            if os.path.exists(path):
                                os.remove(path)
                    except Exception as e:
                        logging.error(f"Error saving TTL media: {e}")

            if self_data.get("save_pv", 0) == 1:
                try:
                    dst_peer = await resolve_destination_peer(client, self_data.get("dst_pv", "me"))
                    await client.forward_messages(dst_peer, event.message)
                except Exception:
                    pass

        @client.on(events.MessageEdited(func=lambda e: e.is_private))
        async def anti_edit_handler(event):
            self_data = await db.get_self_bot(user_id)
            if not self_data or self_data.get("anti_edit", 1) != 1:
                return

            user_cache = PV_MESSAGE_CACHE.get(user_id, {})
            if event.id in user_cache:
                cached = user_cache[event.id]
                old_text = cached["text"]
                new_text = event.text or "بدون متن"

                if old_text != new_text:
                    s_name = cached["sender_name"]
                    s_id = cached["sender_id"]
                    dst_peer = await resolve_destination_peer(client, self_data.get("dst_edit", "me"))

                    report = (
                        f"✏️ <b>پیام ویرایش شده در پیوی ( {s_name} - <code>{s_id}</code> )</b>\n"
                        "━━━━━━━━━━━━━━━━━━━━\n\n"
                        f"<b>متن قبلی:</b>\n<i>{old_text}</i>\n\n"
                        f"<b>متن جدید:</b>\n<i>{new_text}</i>\n"
                        "━━━━━━━━━━━━━━━━━━━━"
                    )
                    try:
                        await client.send_message(dst_peer, report, parse_mode="html")
                    except Exception:
                        try:
                            await client.send_message("me", report, parse_mode="html")
                        except Exception:
                            pass

                    cached["text"] = new_text

        @client.on(events.MessageDeleted())
        async def anti_delete_handler(event):
            self_data = await db.get_self_bot(user_id)
            if not self_data or self_data.get("anti_delete", 1) != 1:
                return

            user_cache = PV_MESSAGE_CACHE.get(user_id, {})
            dst_peer = await resolve_destination_peer(client, self_data.get("dst_del", "me"))

            for deleted_id in event.deleted_ids:
                if deleted_id in user_cache:
                    cached = user_cache.pop(deleted_id)
                    s_name = cached["sender_name"]
                    s_text = cached["text"]
                    s_date = cached["date"]
                    
                    report = (
                        "🗑️ <b>پیام حذف‌شده در پیوی:</b>\n"
                        "━━━━━━━━━━━━━━━━━━━━\n\n"
                        f"👤 فرستنده: <b>{s_name}</b>\n"
                        f"🕒 زمان ارسال: <code>{s_date}</code>\n\n"
                        f"💬 <b>متن پیام حذف‌شده:</b>\n<i>{s_text}</i>\n"
                        "━━━━━━━━━━━━━━━━━━━━"
                    )
                    try:
                        await client.send_message(dst_peer, report, parse_mode="html")
                    except Exception:
                        try:
                            await client.send_message("me", report, parse_mode="html")
                        except Exception:
                            pass

        @client.on(events.NewMessage(outgoing=True))
        async def auto_format_outgoing(event):
            if not event.text or event.text.startswith(".") or event.text in ["پنل", "panel", "سیو", "فور", "حذف", "پینگ", "ساعت", "تاریخ"] or event.text.startswith("سیو ") or event.text.startswith("فور ") or event.text.startswith("ادیت ") or event.text.startswith("اسپم ") or event.text.startswith("تگ "):
                return
            self_data = await db.get_self_bot(user_id)
            if not self_data:
                return
            mode = self_data.get("auto_format_mode", "none")
            if mode == "bold" and not event.text.startswith("**"):
                try:
                    await event.edit(f"**{event.text}**")
                except Exception:
                    pass
            elif mode == "italic" and not event.text.startswith("__"):
                try:
                    await event.edit(f"__{event.text}__")
                except Exception:
                    pass
            elif mode == "mono" and not event.text.startswith("`"):
                try:
                    await event.edit(f"`{event.text}`")
                except Exception:
                    pass

        # --- فرمت خودکار دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:بولد|\.autobold|\.ab|autobold|ab)\s+(روشن|خاموش|on|off)$"))
        async def toggle_ab(event):
            act = event.pattern_match.group(1).lower()
            is_on = act in ["on", "روشن"]
            mode = "bold" if is_on else "none"
            await db.update_self_bot(user_id, auto_format_mode=mode)
            await event.edit(f"✍️ **حالت بولد خودکار {'🟢 روشن' if is_on else '🔴 خاموش'} شد.**")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:ایتالیک|\.autoitalic|\.ai|autoitalic|ai)\s+(روشن|خاموش|on|off)$"))
        async def toggle_ai(event):
            act = event.pattern_match.group(1).lower()
            is_on = act in ["on", "روشن"]
            mode = "italic" if is_on else "none"
            await db.update_self_bot(user_id, auto_format_mode=mode)
            await event.edit(f"✍️ **حالت ایتالیک خودکار {'🟢 روشن' if is_on else '🔴 خاموش'} شد.**")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:مونو|کد|\.automono|\.am|automono|am)\s+(روشن|خاموش|on|off)$"))
        async def toggle_am(event):
            act = event.pattern_match.group(1).lower()
            is_on = act in ["on", "روشن"]
            mode = "mono" if is_on else "none"
            await db.update_self_bot(user_id, auto_format_mode=mode)
            await event.edit(f"✍️ **حالت کد/مونو خودکار {'🟢 روشن' if is_on else '🔴 خاموش'} شد.**")

        # --- سوئیچ‌های تایمر و آنتی‌دلیت دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:سیو\s*تایمر|\.savettl|savettl)\s+(روشن|خاموش|on|off)$"))
        async def toggle_savettl_cmd(event):
            val = 1 if event.pattern_match.group(1).lower() in ["on", "روشن"] else 0
            await db.update_self_bot(user_id, save_ttl=val)
            await event.edit(f"🔒 **ذخیره خودکار عکس‌های تایمردار {'🟢 فعال' if val else '🔴 غیرفعال'} شد.**")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:انتی\s*دلیت|\.antidel|antidel)\s+(روشن|خاموش|on|off)$"))
        async def toggle_antidel_cmd(event):
            val = 1 if event.pattern_match.group(1).lower() in ["on", "روشن"] else 0
            await db.update_self_bot(user_id, anti_delete=val)
            await event.edit(f"🗑️ **آنتی‌دلیت پیام‌های پیوی {'🟢 فعال' if val else '🔴 غیرفعال'} شد.**")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:انتی\s*ادیت|\.antiedit|antiedit)\s+(روشن|خاموش|on|off)$"))
        async def toggle_antiedit_cmd(event):
            val = 1 if event.pattern_match.group(1).lower() in ["on", "روشن"] else 0
            await db.update_self_bot(user_id, anti_edit=val)
            await event.edit(f"✏️ **لاگر پیام‌های ویرایش‌شده (آنتی‌ادیت) {'🟢 فعال' if val else '🔴 غیرفعال'} شد.**")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:سیو\s*پیوی|\.savepv|savepv)\s+(روشن|خاموش|on|off)$"))
        async def toggle_savepv_cmd(event):
            val = 1 if event.pattern_match.group(1).lower() in ["on", "روشن"] else 0
            await db.update_self_bot(user_id, save_pv=val)
            await event.edit(f"📥 **سیو پیام‌های پیوی {'🟢 فعال' if val else '🔴 غیرفعال'} شد.**")

        # --- تنظیمات اسم و ساعت دو زبانه ---
        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:تنظیم\s*اسم|\.setname|setname)\s+(.+)$"))
        async def set_my_name_template(event):
            template = event.pattern_match.group(1).strip()
            me = await client.get_me()
            orig_name = db.clean_time_from_string(me.first_name) or "User"
            
            await db.update_self_bot(user_id, name_template=template, original_name=orig_name)
            
            self_data = await db.get_self_bot(user_id)
            styled_time = get_styled_time(self_data.get("clock_font", "persian") if self_data else "persian")
            preview_name = template.replace("{clock}", styled_time) if "{clock}" in template else f"{template} {styled_time}"
            
            await client(functions.account.UpdateProfileRequest(first_name=preview_name[:64]))
            await event.edit(
                "✅ <b>الگوی جدید ساعت در اسم ست شد:</b>\n"
                f"🏷 <code>{template}</code>\n\n"
                f"👀 <b>پیش‌نمایش زنده:</b> <b>{preview_name}</b>",
                parse_mode="html"
            )

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:حذف\s*ساعت\s*اسم|\.resetname|resetname)$"))
        async def reset_my_name(event):
            me = await client.get_me()
            clean_n = db.clean_time_from_string(me.first_name) or "User"
            await db.update_self_bot(user_id, clock_name=0, clock_bio=0, name_template=clean_n, original_name=clean_n)
            await client(functions.account.UpdateProfileRequest(first_name=clean_n))
            await event.edit("✅ <b>ساعت از روی اسم و بیو حذف شد.</b>", parse_mode="html")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:موزیک|آهنگ|\.find|\.music|music|find)\s+(.+)$"))
        async def music_finder(event):
            q = event.pattern_match.group(1).strip()
            await event.edit(f"🔍 <i>در حال جستجوی موزیک:</i> <code>{q}</code>...", parse_mode="html")
            tracks = await search_music_online(q)
            if not tracks:
                await event.edit(f"❌ موزیکی برای «<code>{q}</code>» پیدا نشد.", parse_mode="html")
                return
            t = f"🎵 <b>نتایج جستجوی آنلاین موزیک برای «{q}»:</b>\n━━━━━━━━━━━━━━━━━━━━\n\n"
            for idx, tr in enumerate(tracks, 1):
                t += f"{idx}. <b>{tr.get('artist',{}).get('name','خواننده')}</b> - {tr.get('title','آهنگ')}\n🔗 <a href='{tr.get('preview','')}'>شنیدن و دانلود پیش‌نمایش</a>\n\n"
            t += "━━━━━━━━━━━━━━━━━━━━"
            await event.edit(t, parse_mode="html")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:ساعت\s*اسم|ساعت\s*بیو|\.name|\.bio|name|bio)\s+(روشن|خاموش|on|off)$"))
        async def clock_toggle(event):
            text_first = event.text.split()[0].replace(".", "").lower()
            act = event.pattern_match.group(1).lower()
            val = 1 if act in ["on", "روشن"] else 0
            if "اسم" in text_first or text_first == "name":
                await db.update_self_bot(user_id, clock_name=val)
                await event.edit(f"🕒 ساعت اسم {'🟢 روشن' if val else '🔴 خاموش'} شد.")
            else:
                await db.update_self_bot(user_id, clock_bio=val)
                await event.edit(f"📝 ساعت بیو {'🟢 روشن' if val else '🔴 خاموش'} شد.")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:فونت|\.font|font)(?:\s+(.+))?$"))
        async def font_change(event):
            f_name = event.pattern_match.group(1)
            if not f_name:
                await event.edit("💡 برای انتخاب فونت اعداد ساعت، دستور `پنل` را بفرستید و دکمه **🔢 ۳۶ فونت زنده اعداد ساعت** را بزنید یا بنویسید:\n`.font digital` یا `.font bold`")
                return

            f_name = f_name.strip().lower()
            if f_name in TIME_FONTS:
                await db.update_self_bot(user_id, clock_font=f_name)
                styled_sample = get_styled_time(f_name)
                await event.edit(f"🎨 فونت ساعت به <code>{f_name}</code> تغییر یافت.\n👀 پیش‌نمایش: <b>{styled_sample}</b>", parse_mode="html")
            else:
                await event.edit("⚠️ نام فونت نامعتبر است. از داخل دستور `پنل` دکمه فونت را انتخاب کنید.")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:پین|\.pin|pin)$"))
        async def pin_msg(event):
            if event.is_reply:
                r = await event.get_reply_message()
                await client.pin_message(event.chat_id, r.id, notify=True)
                await event.edit("📌 **پیام با موفقیت پین شد.**")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:انپین|\.unpin|unpin)$"))
        async def unpin_msg(event):
            if event.is_reply:
                r = await event.get_reply_message()
                await client.unpin_message(event.chat_id, r.id)
                await event.edit("📌 **پیام از پین خارج شد.**")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:اف\s*کی|\.afk|afk)(?:\s+(.+))?$"))
        async def set_afk(event):
            r = event.pattern_match.group(1) or "در دسترس نیستم"
            await db.update_self_bot(user_id, afk_status=1, afk_reason=r)
            await event.edit(
                "💤 <b>حالت استراحت (AFK) فعال شد.</b>\n"
                f"📝 <b>دلیل:</b> <i>{r}</i>",
                parse_mode="html"
            )

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:خروج\s*اف\s*کی|ان\s*اف\s*کی|\.unafk|unafk)$"))
        async def unset_afk(event):
            await db.update_self_bot(user_id, afk_status=0)
            await event.edit("☀️ <b>از حالت استراحت (AFK) خارج شدید.</b>", parse_mode="html")

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:پینگ|\.ping|ping)$"))
        async def ping_cmd(event):
            start = datetime.datetime.now()
            await event.edit("🏓 <b>Pong!</b>", parse_mode="html")
            end = datetime.datetime.now()
            ms = (end - start).microseconds / 1000
            await event.edit(
                "🏓 <b>Pong!</b>\n"
                f"⚡️ سرعت پاسخ‌دهی سلف: <code>{ms:.1f}ms</code>\n"
                "📡 وضعیت اتصال: <b>آنلاین و پرسرعت 🟢</b>",
                parse_mode="html"
            )

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:ساعت|زمان|تاریخ|\.time|time|\.date|date)$"))
        async def time_cmd(event):
            now = datetime.datetime.now(ZoneInfo("Asia/Tehran"))
            await event.edit(
                "🕒 <b>ساعت و تقویم رسمی تهران:</b>\n"
                "━━━━━━━━━━━━━━━━━━━━\n\n"
                f"⏰ زمان: <code>{now.strftime('%H:%M:%S')}</code>\n"
                f"📅 تاریخ میلادی: <code>{now.strftime('%Y/%m/%d')}</code>\n"
                f"🗓 روز: <b>{now.strftime('%A')}</b>\n"
                "━━━━━━━━━━━━━━━━━━━━",
                parse_mode="html"
            )

        @client.on(events.NewMessage(outgoing=True, pattern=r"^(?:حساب|ماشین\s*حساب|\.calc|calc)\s+(.+)$"))
        async def calc_cmd(event):
            try:
                expr = event.pattern_match.group(1).strip().replace("×", "*").replace("÷", "/")
                res = eval(expr, {"__builtins__": None}, {})
                await event.edit(
                    "🧮 <b>ماشین‌حساب هوشمند:</b>\n\n"
                    f"📝 عبارت: <code>{expr}</code>\n"
                    f"📊 نتیجه: <b>{res}</b>",
                    parse_mode="html"
                )
            except Exception:
                await event.edit("❌ عبارت ریاضی نامعتبر است.")

        return True

    except (AuthKeyUnregisteredError, SessionRevokedError, UserDeactivatedError):
        await db.delete_self_bot(user_id)
        if user_id in ACTIVE_CLIENTS:
            del ACTIVE_CLIENTS[user_id]
        return False
    except Exception as e:
        logging.error(f"Userbot error for {user_id}: {e}")
        return False

async def stop_userbot_client(user_id: int):
    if user_id in ACTIVE_CLIENTS:
        try:
            await ACTIVE_CLIENTS[user_id].disconnect()
        except Exception:
            pass
        del ACTIVE_CLIENTS[user_id]

async def clock_background_task():
    from aiogram import Bot
    while True:
        try:
            active_bots = await db.get_all_active_self_bots()
            hourly_price = await db.get_int_setting("self_hourly_price", 600)
            cost_per_minute = max(1, int(hourly_price / 60))

            for b in active_bots:
                uid = b["user_id"]
                user = await db.get_user(uid)
                bal = user["balance"] if user else 0

                if bal < cost_per_minute:
                    await stop_userbot_client(uid)
                    await db.update_self_bot(uid, is_active=0)
                    try:
                        main_b = Bot(token=config.BOT_TOKEN)
                        await main_b.send_message(
                            chat_id=uid,
                            text="⚠️ <b>موجودی کیف پول شما به اتمام رسید!</b>\nسلف بات شما به صورت خودکار متوقف شد. لطفاً جهت فعال‌سازی مجدد، حساب خود را شارژ فرمایید.",
                            parse_mode="HTML"
                        )
                        await main_b.session.close()
                    except Exception:
                        pass
                    continue

                await db.update_balance(uid, -cost_per_minute)

                client = ACTIVE_CLIENTS.get(uid)
                if not client or not client.is_connected():
                    started = await start_userbot_client(uid, b["session_string"])
                    if not started:
                        continue
                    client = ACTIVE_CLIENTS.get(uid)

                styled_time = get_styled_time(b.get("clock_font", "persian"))

                if b.get("clock_name", 0) == 1:
                    try:
                        me = await client.get_me()
                        template = b.get("name_template")
                        if not template or "{clock}" not in template:
                            orig_n = db.clean_time_from_string(b.get("original_name") or me.first_name) or "User"
                            template = f"{orig_n} {{clock}}"

                        new_name = template.replace("{clock}", styled_time)
                        await client(functions.account.UpdateProfileRequest(first_name=new_name[:64]))
                    except Exception:
                        pass

                if b.get("clock_bio", 0) == 1:
                    try:
                        raw_bio = b.get("original_bio") or "PuLaSeLf User"
                        base_bio = db.clean_time_from_string(raw_bio) or "PuLaSeLf"
                        new_bio = f"{base_bio} | {styled_time}"
                        await client(functions.account.UpdateProfileRequest(about=new_bio[:70]))
                    except Exception:
                        pass

        except Exception as e:
            logging.error(f"Clock error: {e}")

        await asyncio.sleep(60)

async def start_all_active_userbots():
    bots = await db.get_all_active_self_bots()
    for b in bots:
        asyncio.create_task(start_userbot_client(b["user_id"], b["session_string"]))
    asyncio.create_task(clock_background_task())
EOF
```

---

دستور بالا رو اجرا و ری‌استارت کن. 
الان می‌تونی با هر استیکر و متنی مثل `.setname ❄️ Frozen Mamad {clock} ❄️` اسمت رو تنظیم کنی و بعد بری از پنل هر فریمی انتخاب کنی؛ استیکرهای ❄️ اول و آخر اسمت دست‌نخورده می‌مونن و کادر فقط میاد دور ساعتت!

تغییر بعدی رو بگو تا بریم سراغش داداش!
