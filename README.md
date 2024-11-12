# Présentation JRES 2024

Pour obtenir le pdf (avec pandoc + miktex sur windows).

<https://github.com/alexeygumirov/pandoc-beamer-how-to>

```sh
pandoc -s --dpi=300 --slide-level 2 --toc --listings --shift-heading-level=0 --data-dir="pandoc" --pdf-engine "lualatex" -f markdown+pipe_tables+yaml_metadata_block+implicit_figures+smart+fenced_divs+strikeout+emoji+raw_attribute -V classoption:aspectratio=169 --template default_mod.latex -t beamer -o prez.pdf .\prez.md
```

* Utilisation du theme [`moloch`](https://github.com/jolars/moloch)
* Font Noto de google (+ Noto Emoji)
* Ajout des vidéos (sans flash) grace à [cette réponse sur stackexchange](https://tex.stackexchange.com/a/516102)