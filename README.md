# BIN_CHD_Converter

Convertisseur de bureau portable par lots pour les images de disques optiques et de disques durs.

[![Build](https://github.com/Endymi0n74/BIN_CHD_Converter/actions/workflows/next-release.yml/badge.svg)](https://github.com/Endymi0n74/BIN_CHD_Converter/actions/workflows/next-release.yml)
[![Latest release](https://img.shields.io/github/v/release/Endymi0n74/BIN_CHD_Converter)](https://github.com/Endymi0n74/BIN_CHD_Converter/releases/latest)
[![License](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](LICENSE.txt)

**🇫🇷 Français** · [🇬🇧 English](README.en.md)

## Ce qu'il fait

BIN_CHD_Converter convertit les images de disque en [CHD](https://docs.mamedev.org/tools/chdman.html) et extrait les fichiers CHD vers des images utilisables. Le client moderne est construit avec Tauri/Rust et utilise des sidecars natifs ; les archives portables ne nécessitent donc pas le runtime .NET.

- Conversion par lots avec analyse récursive des dossiers.
- Progression en temps réel, journaux, temps restant estimé et annulation.
- Routage automatique CD/DVD/HDD en fonction du type de fichier et du contenu de l'image.
- Extraction des CHD vers BIN/CUE, ISO ou IMG.
- Extraction sans collision : les fichiers existants ne sont jamais écrasés.
- Gestion sécurisée des chemins Windows non ASCII et trop longs.
- Contrôle préalable de l'alignement des secteurs avec des messages clairs indiquant les éléments ignorés.
- Archives portables pour Windows, macOS et Linux.

## Formats d'entrée pris en charge

| Type | Extensions | Notes |
|---|---|---|
| CD / GD-ROM | `.cue`, `.bin`, `.raw`, `.ccd`, `.mds`, `.ecm` | Les images multi-pistes et à secteurs bruts sont prises en charge lorsque leurs fichiers compagnons sont disponibles. |
| DVD | `.iso` | Orienté vers `createdvd`. |
| Hard disk | `.img` | Orienté vers `createhd` sauf si la détection du contenu identifie un CD brut. |
| Console | `.gdi`, `.pbp` | Images Dreamcast et PlayStation. |
| Compressed | `.cso`, `.ciso` | Décodées via l'assistant de format natif. |
| Archives | `.zip`, `.7z`, `.rar` | Les archives sont décompactées dans un espace de travail temporaire. |
| Output | `.chd` | L'extraction CHD prend en charge les cibles CD, DVD et HDD. |

La conversion ECM et MDS est assurée par le sidecar NativeAOT `batch-format-helper`. Les ensembles MDS doivent conserver leurs fichiers `.mdf` et leurs fichiers de données divisés à côté du descripteur `.mds`.

## Téléchargement

Téléchargez la dernière archive portable depuis la [page des versions](https://github.com/Endymi0n74/BIN_CHD_Converter/releases/latest) :

- `BIN_CHD_Converter-win-x64-portable.zip`
- `BIN_CHD_Converter-win-arm64-portable.zip`
- `BIN_CHD_Converter-osx-x64-portable.tar.gz`
- `BIN_CHD_Converter-osx-arm64-portable.tar.gz`
- `BIN_CHD_Converter-linux-x64-portable.tar.gz`
- `BIN_CHD_Converter-linux-arm64-portable.tar.gz`

Les builds macOS et Linux sont actuellement publiés en tant qu'artefacts portables « au mieux », non testés. Ce ne sont pas des paquets d'installation. Sur les systèmes Unix, rendez l'application exécutable avant de la lancer :

```sh
chmod +x BIN_CHD_Converter batch-format-helper-*
./BIN_CHD_Converter
```

Chaque archive portable Windows inclut un `chdman.exe` natif correspondant issu de MAME, son `SDL2.dll` requis le cas échéant, et `batch-format-helper`. L'application recherche d'abord dans son propre répertoire, puis utilise `chdman` sur le `PATH` en secours. Vous n'avez pas besoin d'installer MAME séparément pour les archives Windows publiées. L'intégration continue effectue également une conversion de test ISO vers CHD réelle avec l'exécutable fourni.

## Compilation portable pour Windows

Les archives Windows sont autonomes et ne nécessitent pas le runtime .NET. Extrayez une archive, conservez ses fichiers ensemble et lancez `BIN_CHD_Converter.exe`.

Le dépôt conserve également l'ancien client WPF sous `BatchConvertToCHD/` ; le client Tauri est l'application multiplateforme recommandée.

## Compilation à partir des sources

Prérequis :

- .NET SDK 10
- Node.js 24 et npm
- Rust stable et Cargo
- Prérequis de compilation Tauri 2 pour la plateforme cible
- MAME `chdman` uniquement pour exécuter les tests de conversion en local ; les archives publiées l'incluent déjà

Compiler et tester les composants .NET :

```sh
dotnet test BatchConvertToCHD.Tests/BatchConvertToCHD.Tests.csproj
```

Compiler le front-end et le client Rust :

```sh
cd next-app
npm ci
npm run build
cargo build --release --manifest-path src-tauri/Cargo.toml
```

Compiler l'assistant de format NativeAOT :

```sh
dotnet publish Next.FormatHelper/Next.FormatHelper.csproj -c Release -r win-x64 --self-contained true
```

## Assistant de format en ligne de commande

Le sidecar peut être appelé directement :

```text
batch-format-helper <pbp|ccd|cso|ecm|mds> <input> <output-directory>
```

Il affiche le chemin du fichier convertible généré sur la sortie standard et les diagnostics sur la sortie d'erreur standard.

## Notes de sécurité

- Les fichiers sources ne sont supprimés qu'après une conversion réussie si la suppression est activée.
- Les sorties partielles sont supprimées après une opération `chdman` échouée ou annulée.
- L'extraction est redirigée vers un sous-répertoire numéroté lorsque la destination existe déjà.
- Les images dont l'alignement des secteurs est invalide sont ignorées avant la conversion.
- Les artefacts macOS/Linux n'ont pas été testés en local ; les contributions et les rapports sont les bienvenus.

## Contributions et assistance

Veuillez ouvrir un ticket sur [GitHub Issues](https://github.com/Endymi0n74/BIN_CHD_Converter/issues) en indiquant le système d'exploitation, l'architecture, le format d'entrée et la sortie de journal pertinente.

## Licence

BIN_CHD_Converter est distribué sous la GNU General Public License v3.0. Voir [LICENSE.txt](LICENSE.txt).

Le projet utilise ou intègre CHDSharp, CSOSharp, PBPSharp, CCDSharp, SharpCompress, Tauri et MAME/chdman. Consultez les sources et les métadonnées des paquets pour leurs licences et mentions respectives.
