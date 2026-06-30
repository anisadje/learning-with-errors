# Learning With Errors (LWE) — implémentation et analyse expérimentale

> Implémentation *from scratch* d'un cryptosystème symétrique fondé sur le problème **Learning With Errors**, accompagnée d'une étude empirique de l'impact des paramètres ($\sigma$, $m$, $n$, $q$) sur la fiabilité du déchiffrement et la sécurité.

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![NumPy](https://img.shields.io/badge/NumPy-✓-013243)
![License](https://img.shields.io/badge/License-MIT-green)

*Projet réalisé dans le cadre d'un apprentissage personnel.*

---

## Contexte

Les cryptosystèmes à clé publique actuels (RSA, ECC) reposent sur la difficulté de la factorisation et du logarithme discret; deux problèmes que l'algorithme de Shor casse en temps polynomial sur un ordinateur quantique. La cryptographie post-quantique cherche des problèmes résistants : **LWE** (Regev, 2005) en est l'un des piliers, à la base du standard NIST **ML-KEM / Kyber** (FIPS 203).

Ce notebook construit, pas à pas et sans bibliothèque cryptographique, une instance LWE en clé secrète, puis explore expérimentalement la frontière entre **bruit suffisant pour masquer le secret** et **bruit trop élevé pour déchiffrer**.

## Contenu du notebook

| Partie | Sujet |
|---|---|
| **1. Introduction** | Rupture quantique, vulnérabilité de RSA/ECC, émergence de LWE |
| **2. Briques élémentaires** | Échantillonnage gaussien discret, génération de clés, chiffrement/déchiffrement d'un bit |
| **3. Validation expérimentale** | Test sur instance isolée puis sur 100 messages |
| **4. Analyse paramétrique** | Transitions de phase selon $\sigma$, $m$, $n$, $q$ ; analyse croisée $(m, \sigma)$ par heatmap |
| **5. Bilan & ouverture** | Comparaison RSA vs LWE, limites, ouverture vers Ring-LWE / Kyber |

## Résultats clés

- **Le bruit est indispensable** : à $\sigma = 0$, le système se résout instantanément par pivot de Gauss. Le bruit gaussien transforme une distribution en cloche (sur $e$) en une distribution quasi-uniforme (sur $b$), ce qui masque le secret.
- **Transition de phase nette sur $m$** (nombre d'équations) : en dessous d'un seuil, le taux de succès est instable ; au-delà, la redondance permet de tolérer un bruit plus élevé.
- **$n$ joue sur la sécurité, pas sur la fiabilité** : faire varier la dimension du secret laisse les courbes de déchiffrement quasi superposées; $n$ est le paramètre de sécurité.
- **$q$ dicte la tolérance au bruit** : un grand modulo « offre de l'espace » au bruit avant qu'il ne franchisse le seuil de décision $\lfloor q/4 \rfloor$.

## Exécution

### En ligne (recommandé, zéro installation)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/anisadje/learning-with-errors/blob/main/LWE.ipynb)

### En local
```bash
git clone https://github.com/anisadje/learning-with-errors.git
cd learning-with-errors
pip install -r requirements.txt
jupyter notebook LWE.ipynb
```

## Limites connues & pistes

- **Paramètres-jouets** : les valeurs choisies ($n=2$, $q=11$) ont pour but la visualisation, cela ne représente pas la sécurité réelle; l'espace des secrets ($q^n = 121$) se brute-force trivialement. 
- **Échecs de déchiffrement** (*decryption failures*) : ~2 % observés à certains réglages.
- **Variante symétrique** : ce projet implémente LWE à clé secrète, pas le schéma à clé publique complet.
- **Ouverture** : la taille quadratique de la matrice $A$ motive le passage à **Ring-LWE** ($\mathbb{Z}_q[X]/(X^n+1)$) et à **Kyber/ML-KEM**; direction naturelle d'extension du projet.

## Stack technique

Python · NumPy · Matplotlib · Seaborn

## Références principales

Regev (2005), *On lattices, learning with errors…* (JACM) · Shor (1994) · NIST FIPS 203 (2024) · Micciancio, CSE 208 (UC San Diego). *Bibliographie complète en fin de notebook.*

## Auteure

**Anîsa Djedje** — [GitHub](https://github.com/anisadje)
