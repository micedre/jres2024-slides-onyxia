---
title: "Onyxia: une plateforme opensource orientée datascience"
author: 
 - Cédric Couralet
 - Alexis Guyot
 - Olivier Levitt
topic: "Onyxia, kubernetes, datascience"
theme: "moloch"
colortheme: "seahorse"
fonttheme: "professionalfonts"
mainfont: NotoSans
mainfontfallback:
  - "NotoColorEmoji:mode=harf"
fontsize: 12pt
linkstyle: bold
aspectratio: 169
titlegraphic: img/logo-jres-mini1.png
logo: img/logo-jres-mini2.png
lang: fr-FR
section-titles: false
toc: true
header-includes: |
  \usepackage{media9}
  \molochset{titleformat=allsmallcaps,numbering=none,progressbar=frametitle,sectionpage=none}
  
---

# Modernisation de l'offre scientifique du GENES

## Le GENES

::: columns

:::: {.column width=60%}

- Groupe des écoles nationales d'économie et statistique

- 2 écoles d’ingénieurs, un centre de formation continue, un centre de recherche (UMR)

- Tutelle Technique: Insee.

- Domaines :  Statistique, économie, sociologie…

- **Fort penchant vers la science de la données**

::::

:::: column

![](img/logos-genes-transparent.png){ height=50% }

::::

:::

## Offre scientifique historique

::: columns

:::: column

- Serveurs publics/privés

- Baie de stockage

- Logiciels déployés sur partage réseau

::::

:::: column

![](img/offre-hpc-genes.drawio.png){height=40%}

::::

:::

###

😡 Allocation/partage des ressources

😡 Gestion des packages (r, python...)

😡 Gestion des mise à jour (serveur/application)

😑 Performances

😑 ...« je veux faire du LLM »

😑 Reproductibilité / Science Ouverte

## Nouvelle solution

- Moins couteuse pour l'exploitation
- Proposant une meilleure allocation des ressources et leur partage
- Favorisant la reproductibilité
- Favorisant les bonnes pratiques de séparation code/data/traitement

➡️

![](img/kube-git-s3-transparent.png){height=50%}

## Mais

**Comment embarquer les utilisateurs ?**

![](img/chercheur-perplexe-transparent.png){height=50%}

# La plateforme Onyxia

## Onyxia

![](img/logoOnyxia.png){height=20%} 

**Un cloud opensource pour la datascience** (By Insee)

###

- Pas d'enfermement dans une solution
- Cloud Native
- 100% Open Source (MIT)
- Déploiement facile


## Onyxia, c'est quoi ?

![](img/interface-onyxia.png){height=50%}



- Une application web permettant le déploiement de service sur cluster kubernetes
- Un catalogue de services spécialement construit pour l'intégration de services externes (Stockage Objet, Gestion de secrets, Git...)

## Démo

```{=latex}
\embedvideo{\includegraphics[page=1,height=0.7\textheight]{img/interface-onyxia.png}}{img/test.mp4}
```

## Fonctionnement: configuration des services

* Utilisation de helm pour le lancement des services
* Utilisation du `values.schema.json` pour l'affichage du formulaire

```json
"accessKeyId": {
  "description": "AWS Access Key",
  "type": "string",
  "x-form": {
    "value": "{{s3.AWS_ACCESS_KEY_ID}}"
  },
  "hidden": {
    "value": false,
    "path": "s3/enabled"
  }
}
```

## Fonctionnement: mécanisme de découverte

Utilisation de la fonction `lookup` de Helm

```go
{{ range $index, $secret := (lookup "v1" "Secret" $namespace "").items }}
{{- if (index $secret "metadata" "annotations") }}
{{- if and (index $secret "metadata" "annotations" "onyxia/discovery") (eq "mongodb" (index $secret "metadata"
"annotations" "onyxia/discovery" | toString)) }}
```

## Autres fonctionnalités 

* Gestion de projet/groupe
* Possibilité d'avoir son propre catalogue (*simple* dépot de chart Helm)
* Restriction sur le lancement de service 

```yaml
 roles:
        - roleName: vip
          files:
            - relativePath: nodeSelector-gpu.json
              content: |
                {
                  "$schema": "http://json-schema.org/draft-07/schema#",
                  [...]
                      "default": "NVIDIA-A2",
                      "enum": ["NVIDIA-A2", "Tesla-T4", "NVIDIA-H100-PCIe"]
                    }
                  },
                  "additionalProperties": false
                }    
```

# L'instance SSPCLOUD

###

* Instance d'onyxia maintenue par l'Insee
* Expérimentation sur données non sensible, partage de connaissances (formations)
* Ouverte d'abord au data scientist du service statistique public, étendue à l'ensemble des agents de l'état et aux étudiants

###

* Bac à sable ML/IA pour la statistique publique européenne et canadienne
* 1000 utilisateurs quotidiens (7000 inscrits)


# Installation et utilisation d'Onyxia au GENES

### Création d'un datalab

* Service de gestion du code ➡️ gitea
* Service de stockage ➡️ minio
* Accessible depuis l'ensemble des utilisateurs du GENES ➡️ OIDC
* Plateforme d'execution des traitements ➡️ Kubernetes
* Point d'entrée ➡️ Onyxia

### Kubernetes

