# Initialize mkdocs
python3 -m venv venv
source venv/bin/activate    
pip install mkdocs mkdocs-material

mkdocs new .
mkdocs serve
http://127.0.0.1:8000/grass-gis

# Building site: When finished editing, build a static site from Markdown files:
mkdocs build

# Remove git files from the folder
rm -rf .git


# Initialize Git Repo
git add .
git commit -m "first commit"
git branch -M main
git push -f origin main 

# Deploy to GitHub Pages
mkdocs gh-deploy

https://amanchry.github.io/grass-gis


https://grass.osgeo.org/grass85/manuals/grass_database.html


