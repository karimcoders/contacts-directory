# Bihar Data Explorer (password-protected)

Live: https://karimcoders.github.io/contacts-directory/bihar/

8,666 Bihar records (Farmers 5,078 · RTE 816 · Trainees 2,772) — search, filter,
sort, detail sheet, CSV export, Aadhaar reveal.

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
