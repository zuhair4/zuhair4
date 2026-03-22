# .github/workflows/profile-assets.yml
# Place this file at: .github/workflows/profile-assets.yml

name: Generate Profile Assets

on:
  schedule:
    - cron: "0 2 * * *"   # Runs daily at 2:00 AM UTC
  workflow_dispatch:        # Allows manual trigger from Actions tab
  push:
    branches: [main]

jobs:

  # ── 1. Contribution Snake ──────────────────────────────────────────
  snake:
    name: 🐍 Contribution Snake
    runs-on: ubuntu-latest
    steps:
      - name: Generate Snake SVG
        uses: Platane/snk@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/github-snake.svg
            dist/github-snake-dark.svg?palette=github-dark

      - name: Push to output branch
        uses: crazy-max/ghaction-github-pages@v3
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  # ── 2. 3D Contribution Calendar ───────────────────────────────────
  3d-contrib:
    name: 🌌 3D Contribution Calendar
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Generate 3D Contribution Graph
        uses: yoshi389111/github-profile-3d-contrib@0.7.1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          USERNAME: ${{ github.repository_owner }}
          MAX_REPOS: 10
          SETTING_JSON: |
            {
              "githubUserName": "${{ github.repository_owner }}",
              "title": "Zuhair Abbas",
              "topLangs": 5,
              "type": "rainbow"
            }

      - name: Commit and push
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: "chore: update 3D contribution graph"
          file_pattern: profile-3d-contrib/
