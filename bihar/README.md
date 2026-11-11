# Bihar Data Explorer (password-protected)

Live: https://karimcoders.github.io/contacts-directory/bihar/

1,01,335 Bihar records (Kisan Directory 73,885 · KVK Farmers 5,078 · Contacts 18,784 · RTE 816 · Trainees 2,772) — search, filter, sort, detail sheet, CSV export, Aadhaar reveal.

Kisan records me naam + gaon + block + mobile hai (Saran ke 20 blocks). Payload
gzip karke encrypt hota hai, isliye 1 lakh records ke baad bhi page ~2 MB ka hai.

## Types

| Type | Kitne | Kahan se |
|---|---|---|
| Kisan | 73,885 | 20 `full-*.pdf` Kisan Directory (text layer, exact) |
| Farmer | 5,078 | KVK farmer list |
| Contact | 18,784 | 62 BLO / police / bank / business / office lists — Google Contacts print PDFs |
| RTE | 816 | RTE list |
| Trainee | 2,772 | Trainee list |

Contact records me sirf wahi fields hain jo PDF me hain: naam + mobile, aur
kahin-kahin gaon/block (file ke naam se exact match). Jahan PDF me gaon nahi
tha, wahan `—` hi rehta hai — koi andaaza nahi lagaya jata.

## OCR

62 contact PDFs me se 9 me text layer tha (exact parse). 53 image-only the,
unke liye Windows OCR do DPI pe chala:

1. 200 dpi — poora coverage (~2,400 pages)
2. 300 dpi — clarity ke liye, cross-check

PHP merging priority: **PDF text layer > 300 dpi OCR > 200 dpi OCR**, phir
10-digit valid mobile (6-9 se shuru) par dedupe. `Phone`/`Mobile` label ko
kabhi naam nahi maana jata.

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

## Note

Ye folder GitHub repo me hai, isliye repo/account ki security bhi matter karti
hai — 2-step verification on rakho.
