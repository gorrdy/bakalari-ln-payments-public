# ⚡ Bakaláři známky na Lightning platby (LNBits)

Tento projekt automaticky proplácí známky z Bakalářů (přes veřejné API) do Lightning Network peněženky LNBits. Systém je navržen tak, aby:
* Běžel automaticky každý týden pomocí **GitHub Actions**.
* Zajišťoval trvalost zůstatku (dluhu/kreditu) pomocí **GitHub Repository Variables**.
* Umožnil nastavení pozitivních i negativních odměn pro každou známku.

---

## ⚙️ 1. Konfigurace GitHub Secrets (Tajné klíče)

Veškeré citlivé údaje musí být nastaveny jako **Secrets** (Tajné klíče) v nastavení Vašeho repozitáře (Settings > Secrets and variables > Actions).

| Název Secret | Popis |
| :--- | :--- |
| **`GH_API_TOKEN`** | **Kritické:** Váš Personal Access Token (PAT) s oprávněními **`repo`** a **`workflow`**. Tento token řeší chyby 403 a zajišťuje, že skript může číst a zapisovat stav do **GitHub Variables**. |
| `BAKALARI_BASE_URL` | Kompletní adresa Vašeho Bakaláři serveru (např. `https://server.gym-bohumin.cz/`). |
| `BAKALARI_USERNAME` | Uživatelské jméno pro přihlášení do Bakalářů. |
| `BAKALARI_PASSWORD` | Heslo k účtu. |
| `LNBITS_WITHDRAW_KEY` | Váš Withdraw API Key z LNBits peněženky. |
| `LNBITS_HOST` | Adresa Vašeho LNBits serveru (např. `https://lndbits.cz/`). |
| `SMTP_HOST` | Server pro odesílání e-mailů s fakturami. |
| `SMTP_USER` | Uživatelské jméno pro odesílání e-mailů. |
| `SMTP_PASS` | Heslo pro odesílání e-mailů. |
| `NOTIFICATION_EMAIL` | E-mail, kam má být odeslána notifikace/faktura. |

### 🛠️ Jak vytvořit Personal Access Token (PAT)

PAT token potřebujete, protože systémový GitHub Token je pro čtení/zápis proměnných z kódu omezený.

1.  **Přístup:** Přejděte do nastavení svého profilu na GitHubu: **Settings** -> **Developer settings** -> **Personal access tokens** -> **Tokens (classic)**.
2.  **Generování:** Klikněte na **Generate new token (classic)**.
3.  **Nastavení:**
    * **Pojmenujte** ho (např. `Bakalari_State_Access`).
    * **Scopes (Rozsahy):** Zaškrtněte **Kritická oprávnění**:
        * ✅ **`repo`** (Full control of private repositories)
        * ✅ **`workflow`** (Access workflow run status)
        * ✅ **`Zadejte platnost oprávnění`** (90 dní, neurčito apod.)
      
4.  **Uložení:** Zkopírujte vygenerovanou hodnotu a uložte ji jako Secret **`GH_API_TOKEN`** v repozitáři.

---

## 💰 2. Nastavení odměn a parametrů (GitHub Variables)

Odměny v CZK a další parametry se nastavují jako **Variables** (Proměnné) v nastavení repozitáře (Settings > Secrets and variables > Actions).

| Název Variable | Popis | Příklad |
| :--- | :--- | :--- |
| `REWARD_GRADE_1_CZK` | Odměna za známku 1. | `+100` |
| `REWARD_GRADE_2_CZK` | Odměna za známku 2. | `+50` |
| `REWARD_GRADE_3_CZK` | Odměna za známku 3. | `0` |
| `REWARD_GRADE_4_CZK` | Penalizace/Dluh za známku 4. | `-50` |
| `REWARD_GRADE_5_CZK` | Penalizace/Dluh za známku 5. | `-100` |
| `EXCHANGE_RATE_API_URL` | Volitelné: URL pro získání kurzu CZK/BTC. | `https://api.coindesk.com/v1/bpi/currentprice/BTC.json` |

---

## 🔒 3. Nastavení GitHub Actions (Globální Oprávnění)

Aby mohl celý Váš workflow (nejen Váš kód) zapisovat do repozitáře, musíte povolit oprávnění v nastavení celého projektu.

1.  Přejděte do **Settings** (Nastavení) Vašeho repozitáře.
2.  V levém menu vyberte **Actions** -> **General** (Obecné).
3.  Sjeďte dolů k sekci **Workflow permissions** (Oprávnění pracovního postupu).
4.  Vyberte možnost **Read and write permissions** (Oprávnění ke čtení a zápisu). 
5.  Klikněte na **Save** (Uložit).

> Tato volba zajistí, že vestavěný systémový token (i když ho nepoužíváme přímo pro API volání) má dostatečná práva pro základní operace, jako je stažení repozitáře.

---

## 🚀 4. Spuštění a monitorování

Workflow je nastaveno tak, aby automaticky běželo každý týden v souboru `weekly-payout.yml`.

### První spuštění:

1.  Ujistěte se, že máte nastaveny všechny **Secrets** (včetně **`GH_API_TOKEN`**) a **Variables**.
2.  Přejděte na záložku **Actions** (Akce).
3.  Vyberte Váš workflow (např. "Weekly Bakalari LN Payout").
4.  Klikněte na tlačítko **"Run workflow"** a stiskněte zelené tlačítko pro ruční spuštění.

---

## 💻 5. Pro použivatele

### První automatické spuštění:

Jakmile si nastavíte vlastní Secrets, odkomentujte sekci schelude v souboru weekly-payout.yml, abyste aktivovali týdenní běh. 
Ruční spuštění pro testování povoleno.

 > - cron: "0 12 * * 1" 
 
