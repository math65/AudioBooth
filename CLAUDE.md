# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Projet

Client iOS/watchOS pour serveur **Audiobookshelf** auto-hébergé. Swift 6.2 (concurrence stricte, async/await), SwiftUI + SwiftData, Xcode 26. iOS 17.0+ / watchOS 10.0+. L'app ne fonctionne qu'avec un serveur Audiobookshelf actif ; elle ne contient aucun média.

## Structure (multi-module)

Chaîne de dépendances : `PlayerIntents → Models → API`.

- `API/` — couche réseau. `Sources/API/Services` (services REST), `Sources/API/Models` (DTOs).
- `Models/` — modèles SwiftData persistants, schéma (`AudiobookshelfSchema.swift`), état de lecture.
- `PlayerIntents/` — iOS uniquement, dépend de Models.
- `AudioBooth/AudioBooth.xcodeproj` — projet Xcode. Targets : app principale, `AudioBooth Watch App`, `AudioBoothWidget`, `AudioBoothWatchWidget`.
- App organisée par `Screens/` (dossiers par feature, pattern **View/Model/ViewModel**), `Services/` (managers), `Components/`, `Extensions/`, `AppIntents/`, `CarPlay/`.

## Build / Format / Lint

- **Format** (avant commit) : `xcrun swift-format format --in-place --recursive --parallel .`
- **Lint strict** : `xcrun swift-format lint --strict --recursive --parallel .` (les warnings sont des erreurs)
- **Build (vérification)** : `xcodebuild -project AudioBooth/AudioBooth.xcodeproj -scheme AudioBooth -destination 'generic/platform=iOS Simulator' build`
- **Pre-commit** : `pre-commit install` requis ; lance format + lint strict sur les `.swift` (voir `.pre-commit-config.yaml`).
- **Aucun test** dans le repo (pas de test target, pas de CI). Vérifie via build + lint.

## Style de code (écarts vs défauts swift-format — voir `.swift-format`)

- Indentation **2 espaces** ; blocs `#if` et labels `case` non indentés.
- Longueur de ligne **120**.
- **Un argument de fonction par ligne** (`lineBreakBeforeEachArgument`).
- **Trailing comma** obligatoire dans les collections multi-éléments.
- **Imports triés** alphabétiquement (`OrderedImports`).
- Privacy au niveau fichier : `private` (pas `fileprivate`).
- Identifiants **ASCII uniquement**.
- Force-unwrap / force-try **autorisés** par le linter (pas de règle bloquante).

## Conventions

- **Branches** : `feature/<nom>`, `fix/<nom>`.
- **Historique `main` rebasé / force-push** ("early development") — ne pas supposer un historique stable.
- **Langue du dépôt : anglais.** Messages de commit, titres/descriptions de PR et issues sont rédigés en **anglais** (le projet est upstream international). Le code et les commentaires suivent l'existant.

## Setup / Signing

- Copier `AudioBooth/Local.xcconfig.example` → `Local.xcconfig` (gitignored) pour surcharger `DEVELOPMENT_TEAM` et `ORG_IDENTIFIER`. Inclus via `#include? "Local.xcconfig"`.
- `Local.xcconfig` définit la condition de compilation `CONTRIBUTOR_BUILD` (le code branche dessus).
- Build simulateur OK par défaut. Build sur device avec compte Apple gratuit : retirer manuellement les capabilities (In-App Purchase, NFC, iCloud KV, Access Wi-Fi Information sur la target Watch).
