# Herizon

## Mission
To empower girls, women, and those who identify as female in North Carolina to achieve independence by equipping them with the tools, mentorship, and opportunities to build sustainable careers.

## Directors
Laura Micek
Evan Micek 

## Mentors
Michelle Bayait

## Contact
Email us at support@ontheherizon.org, or please reach out to [Laura Micek](https://www.linkedin.com/in/lauramicek/)

## Documentation

Project documentation lives in `docs/` and is built with [MkDocs](https://www.mkdocs.org/).
Python dependencies are managed with [uv](https://docs.astral.sh/uv/).

```powershell
# one-time: install uv (https://docs.astral.sh/uv/getting-started/installation/)
uv sync                  # create .venv and install MkDocs
uv run mkdocs serve      # preview at http://127.0.0.1:8000
uv run mkdocs build      # build static docs into site/
```

The website itself is plain HTML/CSS in `src/` and is deployed by GitHub Pages.
