# SeleniumBase を使った Webスクレイピング

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/blob/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.jp)

SeleniumBase の高度な機能とステップバイステップガイドを使って、Webスクレイピングをシンプルにします。Selenium を使った Webスクレイピングに興味がありますか？[こちらのガイド](https://brightdata.jp/blog/how-tos/using-selenium-for-web-scraping)をご確認ください。

## SeleniumBase とは？

SeleniumBase は、Selenium/WebDriver API を基盤に構築されたブラウザ自動化の Python フレームワークです。テストからスクレイピングまでのタスクをサポートし、CAPTCHA のバイパスやボット検知回避などの機能を備えています。

## SeleniumBase vs Selenium: 機能と API の比較

| Feature                  | SeleniumBase                                      | Selenium                                    |
|--------------------------|---------------------------------------------------|---------------------------------------------|
| Built-in test runners    | pytest、pynose、behave と統合                      | テスト統合には手動セットアップが必要         |
| Driver management        | 一致するブラウザドライバーを自動ダウンロード       | 手動ダウンロードと設定                      |
| Web automation logic     | 手順を単一メソッド呼び出しにまとめられる           | 複数行のコードが必要                        |
| Selector handling        | CSS または XPath セレクターを自動検出              | セレクター種別を明示する必要がある          |
| Timeout handling         | 失敗を防ぐためのデフォルトのタイムアウト           | 明示的なタイムアウトがないと即時失敗         |
| Error outputs            | クリーンで読みやすいエラーメッセージ               | 冗長で解釈しにくいエラーログ                |
| Dashboards and reports   | ダッシュボード、レポート、スクリーンショット内蔵   | ダッシュボードやレポート機能は内蔵されない  |
| Desktop GUI applications | テスト実行のためのビジュアルツール                 | デスクトップ GUI ツールがない               |
| Test recorder            | テストレコーダー内蔵                               | スクリプトの手動作成が必要                   |
| Test case management     | CasePlans を提供                                   | テストケース管理は内蔵されない              |
| Data app support         | データアプリ向けに ChartMaker を含む               | データアプリ向け追加ツールはない            |

## Webスクレイピングに SeleniumBase を使う方法: ステップバイステップガイド

### Step #1: プロジェクトの初期化

```bash
mkdir seleniumbase-scraper
cd seleniumbase-scraper
python -m venv env
```

仮想環境を有効化します:

- Linux/macOS の場合: `./env/bin/activate`
- Windows の場合: `env/Scripts/activate`

SeleniumBase をインストールします:

```bash
pip install seleniumbase
```

### Step #2: SeleniumBase テストのセットアップ

```python
from seleniumbase import SB

with SB() as sb:
    pass
```

スクリプトを実行します:

```bash
python3 scraper.py --headless
```

### Step #3: 対象ページに接続する

```python
sb.open("https://quotes.toscrape.com/")
```

### Step #4: Quote 要素を選択する

```python
quote_elements = sb.find_elements(".quote")
```

### Step #5: Quote データをスクレイピングする

```python
from selenium.webdriver.common.by import By

for quote_element in quote_elements:
    text_element = quote_element.find_element(By.CSS_SELECTOR, ".text")
    text = text_element.text.replace("“", "").replace("”", "")
    author_element = quote_element.find_element(By.CSS_SELECTOR, ".author")
    author = author_element.text
    tags = [tag.text for tag in quote_element.find_elements(By.CSS_SELECTOR, ".tag")]
```

### Step #6: Quotes 配列を埋める

```python
quotes.append({"text": text, "author": author, "tags": tags})
```

### Step #7: クローリングロジックを実装する

```python
while sb.is_element_present(".next"):
    sb.click(".next a")
```

### Step #8: スクレイピングしたデータをエクスポートする

```python
import csv

with open("quotes.csv", mode="w", newline="", encoding="utf-8") as file:
    writer = csv.DictWriter(file, fieldnames=["text", "author", "tags"])
    writer.writeheader()
    for quote in quotes:
        writer.writerow({"text": quote["text"], "author": quote["author"], "tags": ";".join(quote["tags"])})
```

### Step #9: すべてをまとめる

```python
from seleniumbase import SB
from selenium.webdriver.common.by import By
import csv

with SB() as sb:
    sb.open("https://quotes.toscrape.com/")
    quotes = []
    while sb.is_element_present(".next"):
        quote_elements = sb.find_elements(".quote")
        for quote_element in quote_elements:
            text_element = quote_element.find_element(By.CSS_SELECTOR, ".text")
            text = text_element.text.replace("“", "").replace("”", "")
            author_element = quote_element.find_element(By.CSS_SELECTOR, ".author")
            author = author_element.text
            tags = [tag.text for tag in quote_element.find_elements(By.CSS_SELECTOR, ".tag")]
            quotes.append({"text": text, "author": author, "tags": tags})
        sb.click(".next a")
    with open("quotes.csv", mode="w", newline="", encoding="utf-8") as file:
        writer = csv.DictWriter(file, fieldnames=["text", "author", "tags"])
        writer.writeheader()
        for quote in quotes:
            writer.writerow({"text": quote["text"], "author": quote["author"], "tags": ";".join(quote["tags"])})
```

スクレイパーを実行します:

```bash
python3 script.py --headless
```

## SeleniumBase を使った高度なスクレイピングのユースケース

### フォーム入力と送信を自動化する

```python
from seleniumbase import BaseCase
BaseCase.main(__name__, __file__)

class LoginTest(BaseCase):
    def test_submit_login_form(self):
        self.open("https://quotes.toscrape.com/login")
        self.type("#username", "test")
        self.type("#password", "test")
        self.click("input[type=\"submit\"]")
        self.assert_text("Top Ten tags")
```

テストを実行します:

```bash
pytest login.py
```

### シンプルなアンチボット技術をバイパスする

```python
from seleniumbase import SB

with SB(uc=True) as sb:
    url = "https://www.scrapingcourse.com/antibot-challenge"
    sb.uc_open_with_reconnect(url, reconnect_time=4)
    sb.uc_gui_click_captcha()
    sb.save_screenshot("screenshot.png")
```

### 複雑なアンチボット技術をバイパスする

```python
from seleniumbase import SB

with SB(uc=True, test=True) as sb:
    url = "https://gitlab.com/users/sign_in"
    sb.activate_cdp_mode(url)
    sb.uc_gui_click_captcha()
    sb.sleep(2)
    sb.save_screenshot("screenshot.png")
```

## 結論

SeleniumBase は、アンチボット対策をバイパスするための UC Mode や CDP Mode など、Webスクレイピング向けの高度な機能を提供します。より堅牢なソリューションが必要な場合は、[Bright Data の Scraping Browser](https://brightdata.jp/products/scraping-browser) のようなクラウドベースのブラウザの利用をご検討ください。