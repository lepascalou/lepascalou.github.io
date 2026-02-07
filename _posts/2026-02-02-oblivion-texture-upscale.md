---
title: "Oblivion Texture Upscale"
date: 2026-02-02 21:20:56 +0100
category: [Tech]
tags: [modding,IA,nerd]
description: "Comment améliorer visuellement certaines textures dans Oblivion"
media_subpath: /assets/img/oblivion/
---


## Oblivion et modding

Jeu de rôle en monde ouvert sorti en 2006, après Morrowind (2002) et avant Skyrim (2011). De nombreux outils et mods ont été publiés depuis son lancement. La page Nexus [Oblivion Mods](https://www.nexusmods.com/games/oblivion/mods) recense 32955 mods. 

Parmi tous ces mods, certains améliorent le rendu des textures d'origine soit en les recréant totalement, soit en augmentant la résolution des images de base. Pour cela il existe depuis quelques années des modèles d'IA qui sont de plus en plus performants et qui peuvent ajouter de nombreux détails à des textures en basse résolution.

J'ai choisi l'objet "Fleshy Pod" que l'on trouve dans les plans d'Oblivion pour tester le processus d'upscaling de texture.

![Fleshy Pod](Fleshpod_Vanilla1344x756.webp){: lqip="Fleshpod_Vanilla.webp" }
_Vanilla Fleshy Pod_


## Les outils indispensables, les outils optionnels

Afin d'accéder aux ressources du jeu et les modifier, plusieurs logiciels sont nécessaires. Voici une liste non exhaustive de ceux que j'ai utilisés :
- [BSA Browser](https://www.nexusmods.com/skyrimspecialedition/mods/1756?tab=description) : pour l'extraction des textures du jeu
- [GIMP](https://www.gimp.org/downloads/) ou [Paint.NET](https://www.getpaint.net/download.html): pour l'édition, la création de "normal maps", et la conversion dds/png
- Upscaling, différentes options :
	- [chaiNNer](https://chainner.app/) : open source, gratuit, prise en main relativement simple, permet des opérations complexes, supporte beaucoup de modèles
	- [Upscayl - AI Image Upscaler](https://upscayl.org/) : open source, gratuit. Le nom est assez explicite, très simple d'utilisation.
	- [Topaz Gigapixel](https://www.topazlabs.com/topaz-gigapixel) : propriétaire, simple, efficace, et ... payant !
-  [7-Zip](https://www.7-zip.org/) : pour la compression des fichiers textures en "mods" installables
- [Mod Organizer 2](https://www.modorganizer.org/) : pour l'installation des mods
- [SkyBSA](https://www.nexusmods.com/oblivion/mods/49568) : invalidation des fichiers qui seront installés par le mod
- Optionnels :
	- [Texture Tools Exporter](https://developer.nvidia.com/texture-tools-exporter) : pour les infos de format des textures originales, également pour la conversion vers DDS ou la création de normal maps, simple et efficace
	- [xNormal](https://xnormal.net/) : pour la création des normal maps et la prévisualisation du rendu 3D
	- [ShaderMap - Normal Map Generator](https://shadermap.com/home/) : pour la création des normal maps et la prévisualisation du rendu 3D
	- [NifTools Project](https://www.niftools.org/) : visualisation / édition des mesh du modèle
	- [Blender](https://www.blender.org/download/) : édition des modèles 3D, logiciel pro qui dépasse largement le spectre des modifications de texture, permet le rendu en 3D des modifications.

## Les étapes

### Extraction textures/mesh

La texture que je souhaite modifier est celle de l'objet appelé Fleshy Pod dans le jeu. Depuis BSA Browser, après avoir ouvert l'archive `Oblivion - textures - compressed.BSA` située dans le dossier `Data` du répertoire d'installation du jeu, je fais une recherche du terme `flesh` et en double-cliquant sur le nom des textures le logiciel affiche une fenêtre de rendu. On y voit la résolution et le format de compression, ici 512x512 et DXT1. 

![Captureshot](BSABRenderDDS.webp)
_BSA Browser DDS window_

Quand la texture correspondante est trouvée, cliquer sur 'Extract with folder' pour garder une trace du chemin d'accès d'origine. Par exemple pour le fichier `fleshsack.dds` il s'agit de `textures\oblivion\exteriorcitadel\traps\`{: .filepath}. 
La procédure est la même pour extraire les mesh, ce qui peut être utile pour déterminer les différentes textures utilisées lorsqu'il y en a plusieurs combinées.
J'organise mes repertoires de la sorte :

```
├──Modding Oblivion
  ├──Vanilla
    ├──textures
	  ├──creatures
	    ├── ...
	  ├──oblivion
		├──exteriorcitadel
		  ├──traps
		  ├──...
	  ├──meshes
		  ├──...
  ├──PNGs
  ├──1X
  ├──2X
  ├──4X
```

Il existe plusieurs plusieurs fichiers textures dans le dossier ayant le même nom mais une extension différente, on a dans l'exemple : `fleshsack.dds`, `fleshsack_n.dds`, `fleshtendons.dds` et `fleshtendons_n.dds`. 

Ces fichiers avec le suffixe `_n` sont des "normal map" : il s'agit de la même texture traitée différemment afin de donner des instructions de détail et de profondeur et permettre ainsi au modèle d'avoir du relief et de réagir aux sources de lumière dans le jeu. 

> Ces fichiers sont importants, sans la "normal map" l'objet est plat et ne renvoie pas la lumière. 
{: .prompt-info }

### Conversion en PNG

Certains des logiciels pour l'upscale par IA n'acceptent pas le format `dds` comme entrée, c'est le cas par exemple de l'application Upscayl ou encore Topaz Gigapixel. J'utilise [XnConvert](https://www.xnview.com/fr/xnconvert/) pour convertir par lot au format `png` (pour une compression sans perte de qualité, "lossless").

### Upscaling de la texture principale

Il existe *beaucoup* de modèles différents, entrainés sur des *datasets* divers et avec des fonctionnalités d'upscale ciblées. Le site [OpenModelDB](https://openmodeldb.info/) contient une large collection de modèles et permet de les trier selon les fonctionnalités voulues. La plupart sont utilisables dans chaiNNer.
Pour cet exemple précis, le "Fleshy pod", j'ai trouvé que les meilleurs résultats sont donnés par le modèle "Low Res" de Gigapixel, cependant les résultats avec Upscayl sont très corrects.


#### Seamless maker

Le logiciel [Seamless maker](https://github.com/Seasoned-In-Chaos/seamless-texture-maker) permet de transformer les bords de la texture afin de la rendre "tileable". L'utilisation de cette technique évite d'avoir des raccords de texture apparents en jeu. L'application permet d'ajuster avec finesse les zones de raccord.


#### Conversion en DDS

Le moteur de jeu d'Oblivion supporte les formats de texture BC1/DXT1 et BC3/DXT5. Le premier est utilisé pour la texture principale sans canal alpha (transparence) et le deuxième pour les "normal map" et les "glow map" qui elles ont un canal alpha.

> Il est important de cocher la case `Genérer les mipmaps` pour éviter des artefacts de raccordement de texture en jeu.
{: .prompt-tip }


##### Via GIMP

`Fichier > Exporter sous`, `fleshsack.dds`, `OK` puis dans la liste déroulante choisir BC1.


##### Via Paint.NET

`Fichier > Enregistrer sous`, `fleshsack.dds`, `OK` puis dans la liste déroulante choisir BC1.



##### Via Nvidia Texture Tools Exporter

![Nvidia Texture Tool](NvidiaTTE.webp)

### Création de la normal map

Plusieurs possibilités : Gimp, Paint.NET, xNormal, Shader Map 4, Crazy Bump, Materialize...

Gimp inclut de base une fonctionnalité de génération de normal map dont les contrôles sont assez limités. Le plugin pour Paint.NET est plus abouti et permet des réglages plus fins avec prévisualisation. Crazy Bump et Materialize donnent de bons résultats et permettent des réglages plus poussés.

Pour le plugin de Paint.NET : le télécharger depuis la page [Height to Normal Map](https://forums.getpaint.net/topic/132049-height-to-normal-map-17-nov-2024/#comment-640277) et l'installer en suivant les instructions [Install Plugins](https://www.getpaint.net/doc/latest/InstallPlugins.html). Après avoir désaturé la texture, lancer la génération depuis `Effets > Styliser > Height to Normal Map`. Une fois créée et ajustée exporter via `Enregistrer sous fleshsack_n.dds` puis sélectionner BC3 (linéaire, DXT5).

> Le passage en noir et blanc est important pour la qualité de la normal map.
{: .prompt-tip }

![Sans passage Noir et blanc](normalmapWithoutGS.webp)
_Sans l'étape Noir et Blanc_

![Avec passage Noir et blanc](normalmapWithGS.webp)
_Avec l'étape Noir et Blanc_

### Créer le mod et l'installer

Compresser le dossier `textures` (dans lequel se trouvent les fichiers édités) en 7z avec 7z-gui, lui donner un nom identifiable, par exemple `Fleshsack1K_GigapixelRecover.7z`. La structure du dossier à compresser est `textures\oblivion\citadelexterior\traps\[fleshsack.dds,fleshsack_n.dds]`{: .filepath}. 

Installer depuis Mod Organizer 2 (Ctrl M), activer le mod et lancer le jeu.

## Résultat

![Fleshy Pod](Fleshpod_Upscaled1344x756.webp){: lqip="Fleshpod_Upscaled.webp" }
_Upscaled Fleshy Pod_
