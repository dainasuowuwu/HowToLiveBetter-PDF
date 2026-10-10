name: Generate PDF

on:
  workflow_dispatch: {}

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout this repo
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Clone source project
        run: git clone --depth 1 https://github.com/eternity4719/HowToLiveBetter.git source

      - name: Install pandoc and LaTeX
        run: |
          sudo apt-get update
          sudo apt-get install -y pandoc texlive-xetex texlive-fonts-recommended texlive-latex-extra fonts-noto-cjk

      - name: Generate PDF from Markdown
        run: |
          cd source
          pandoc -s -o "../高性价比人生指南.pdf" \
            --pdf-engine=xelatex \
            -V CJKmainfont="Noto Serif CJK SC" \
            README.md $(ls book/*.md | sort -V)

      - name: Save PDF into repo and push
        run: |
          mv "../高性价比人生指南.pdf" .
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add "高性价比人生指南.pdf"
          if git diff --cached --quiet; then
            echo "No changes to commit"
          else
            git commit -m "Add 高性价比人生指南.pdf"
            git push origin HEAD:main
          fi
