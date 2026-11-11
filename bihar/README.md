# Bihar Data Explorer (password-protected)

Live: https://karimcoders.github.io/contacts-directory/bihar/

1,10,981 Bihar records — search, filter (type / location / **list**), sort, detail sheet, CSV export, Aadhaar reveal.

| Type | Kitne | Kahaan se |
|---|---|---|
| Kisan | 73,885 | 20 `full-*.pdf` Kisan Directory (text layer, exact) |
| Contacts | 28,430 | 62 contact PDFs (text layer + OCR) + voter/teacher/student files + Facebook list |
| Farmers | 5,078 | KVK Saran farmer register |
| Trainees | 2,772 | PM-Surya Ghar trained candidates |
| RTE | 816 | Bihar Gyandeep RTE list |

## Contact lists (28,430)

Har record me sirf wahi fields hain jo source me hain. Jahan gaon/block nahi
tha, wahan `—` hi rehta hai — koi andaaza nahi lagaya jata.

| Source | Records |
|---|---|
| `33.xlsx` voter list (8,000 rows, joint families ek number share karte hain) | 6,734 |
| Patna teacher | 1,907 |
| Police inspector Marhaura | 1,633 |
| Dighwara Block Development office | 1,479 |
| Bihar Vidhan Parishad | 1,429 |
| `Report*.xls` school students | 1,202 |
| Parwaz Baldiah | 959 |
| Maker Jila Parishad | 893 |
| `DIKSHA REPORT.xlsx` teachers | 732 |
| `asoyia BLO.pdf` (Urdu print, RTL numbers) | 404 |
| `samsad-facebook.html` (Facebook page contact list) | 535 |
| ... 53 more lists | — |

App me **"List (PDF / source)"** dropdown hai — usse har PDF ka data alag se
dekh sakte ho (`All lists (65)` ke baad har list + count).

## OCR

62 contact PDFs me se 9 me text layer tha (exact parse). 53 image-only the,
unke liye Windows OCR do DPI pe chala:

1. 200 dpi — poora coverage (~2,400 pages)
2. 300 dpi — clarity ke liye, cross-check

Merge priority: **PDF text layer > 300 dpi OCR > 200 dpi OCR**, phir valid
10-digit mobile (6-9 se shuru) par dedupe. `Phone`/`Mobile` label ko kabhi
naam nahi maana jata.

`asoyia BLO.pdf` (41 pages) Urdu-locale Google Contacts print hai — 2 fonts,
ek ka ToUnicode missing, isliye Roman naam aur numbers page par outline hain
aur sirf OCR se nikalte hain. Layout RTL hai: `+91 85798 65621` chhap kar
`65621 85798 91+` dikhta hai. Groups ka order ulta karke number banaya jata
hai — ye rule doosre sources ke against 9-0 se verify hua. Naam pairing
card-wise (number ke upar wala naam) hoti hai.

`samsad-facebook.html` ek Facebook page ka export hai — 2,338 messages, unme
577 unique numbers. Naam wahin se, jaise hain.

## Ye page password se kaise protected hai

Poora dataset **`index.html` ke andar encrypted hai** — AES-256-GCM, key
PBKDF2-SHA256 se password + random salt se derive hoti hai (250,000 rounds).
File me sirf `{ iter, salt, iv, ct }` hai; plaintext bytes (naam, mobile,
Aadhaar) file me kahin nahi hain.

Isliye GitHub Pages ka public URL hone ke bawajood data public nahi hai —
password ke bina decrypt nahi hota. (Fake JS prompt nahi, real crypto hai.)

## Rebuild / password badalna

Source: `farmer-db/build_secure.py` (local machine par).

```powershell
cd "<repo>\farmer-db"
$env:BIHAR_PASSWORD="naya-strong-password"   # 12+ chars
python build_secure.py                        # -> _secure/index.html
Copy-Item _secure\index.html "<clone>\bihar\index.html" -Force
cd "<clone>"; git add bihar; git commit -m "Rotate password"; git push
```

Password bhool gaye to published copy **recover nahi hoti** — naya build karke
push karna padega (local `data/*.json` se data wapas aa jata hai).

GitHub Pages 1-2 minute purana file serve karta hai — naya data na dikhe to
Ctrl+F5 (hard refresh) karo.

## Note

Ye folder GitHub repo me hai, isliye repo/account ki security bhi matter karti
hai — 2-step verification on rakho.
