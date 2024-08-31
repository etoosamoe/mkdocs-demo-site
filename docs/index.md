# How to create MkDocs site on Github Actions (GHA)

Create a repo in Github. Note that the name of the repo will be in your Github Pages URL.

Clone the repo to your computer and change your dir to it.

Add `mkdocs` or `mkdocs-material` to `requirements.txt`:
`requirements.txt`:
```
mkdocs-material
```

```
python3 -m venv .venv
echo ".venv" >> .gitignore
python3 -m pip install -r requirements.txt
mkdocs new .
```

Set `site_url` parameter in mkdocs.yml to your Github Pages name:
```
site_url: https://<username>.github.io/<repo_name>
```

Add Github Actions workflow:

`.github/workflows/ci.yml`:
```
name: ci 
on:
  push:
    branches:
      - master 
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: 
      name: mkdocs-prod
      url: https://etoosamoe.github.io/mkdocs-demo-site
    steps:
      - uses: actions/checkout@v4
      - name: Configure Git Credentials
        run: |
          git config user.name github-actions[bot]
          git config user.email 41898282+github-actions[bot]@users.noreply.github.com
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV 
      - uses: actions/cache@v4
        with:
          key: mkdocs-material-${{ env.cache_id }}
          path: .cache
          restore-keys: |
            mkdocs-material-
      - run: pip install -r requirements.txt
      - run: mkdocs gh-deploy --force
```

Commit everything to master (main) branch:
```
git add .
git commit 'Init commit'
git push origin master
```

Set up the Github repo:
Settings -> Pages -> Source: Deploy from a branch

It is possible that you will need to add another commit to trigger the workflow again.

Visit your Pages.