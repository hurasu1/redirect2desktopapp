# redirect2desktopapp
https://claudeなどできたものをclaude://に転送する。あ

## 使い方

| 開くもの | リンク |
|---|---|
| Claude | `?to=claude&q=プロンプト` |
| ChatGPT | `?to=chatgpt&q=プロンプト` |
| Asana / Notion / Slack | `?to=asana&url=元のhttpsリンク`（notion・slack も同じ） |
| VS Code（フォルダ・ファイル） | `?to=vscode&path=/絶対パス`、行を指定するなら `&line=10` |
| VS Code（SSH 先） | `?to=vscode&host=10.10.0.119&path=/絶対パス`（host は `~/.ssh/config` の Host 名か IP） |

VS Code は `vscode://file/<パス>`、host があれば `vscode://vscode-remote/ssh-remote+<host><パス>`（Remote-SSH）に転送する。SSH 先では行番号の指定は使わない。絶対パスだけ受け付け、`..` を含むものは開かない。

## GitHub Pages deployment

Before the first deployment, a repository administrator must enable Pages in
**Settings → Pages → Build and deployment → Source → GitHub Actions**.
Then run **Deploy to GitHub Pages** from the Actions tab, or push to `main`.
Without this setup, `actions/configure-pages` fails with `Get Pages site failed`
and `Not Found`.

Alternatively, add a repository Actions secret named `PAGES_SETUP_TOKEN` containing
a fine-grained personal access token with access to this repository and
**Pages: Read and write** permission. The workflow uses it to enable Pages
automatically. The default `GITHUB_TOKEN` cannot perform this initial enablement,
even with `pages: write`. Remove the setup secret after Pages is enabled; subsequent
deployments use `GITHUB_TOKEN`.
