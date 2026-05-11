# Resume

Source for my resume. Auto-builds on push to `main` and publishes to `https://saahil.io/resume.pdf`.

## Layout

```
resume.tex                Single-source LaTeX resume.
.github/workflows/        GitHub Actions: build → S3 → CloudFront invalidation.
```

## Local build

Requires a TeX distribution (TeX Live, MacTeX, MiKTeX) with `latexmk`.

```bash
latexmk -pdf resume.tex
open resume.pdf
```

Clean intermediate files:

```bash
latexmk -c
```

## Deploy pipeline

`.github/workflows/deploy.yml` triggers on every push to `main` that touches `resume.tex` or the workflow itself.

1. `xu-cheng/latex-action` compiles `resume.tex` → `resume.pdf`.
2. PDF uploaded as a workflow artifact (useful for debugging).
3. OIDC assume of the shared deploy role.
4. `aws s3 cp resume.pdf s3://saahil.io-site/resume.pdf` with explicit content-type and short cache.
5. CloudFront invalidation on `/resume.pdf`.

End-to-end ~1 minute.
