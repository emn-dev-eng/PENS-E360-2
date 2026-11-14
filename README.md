# Étheria v0.1 — Exploration culturelle

**La traduction automatique a été supprimée.** Le produit explore les lieux évoqués, traditions, anecdotes, contexte et éléments visibles dans une image fixe. Les bulles sont synchronisées aux timestamps de Whisper.

## Démarrage

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env   # Windows : copy .env.example .env
uvicorn server:app --reload --port 8000
```

Ouvrir http://127.0.0.1:8000. Sans clé API, utiliser **Démonstration**. Pour activer l'analyse IA, ajouter `OPENAI_API_KEY=...` au fichier `.env` (ne jamais le publier). Le modèle Whisper est téléchargé lors du premier lancement et utilise le CPU ; FFmpeg peut être nécessaire selon le format et l'environnement.

## Parcours réel

1. Charger une vidéo **autorisée** et de taille inférieure à 60 Mo.
2. Lancer **Transcription Whisper** (langue d'origine, sans traduction).
3. Lancer **Analyser les scènes** pour les indices textuels, lieux et traditions évoqués.
4. Placer la vidéo à une scène, sélectionner sa transcription, puis **Analyser l'image actuelle** pour transmettre **une seule image fixe** au modèle multimodal.
5. Consulter les bulles, ajouter des références, poser une question et exporter le carnet.

## Limites importantes

- **Voix** : l'analyse acoustique de prosodie et d'émotions n'est **pas implémentée** ; Whisper produit du texte, pas une mesure fiable des émotions.
- **Gestes** : une image fixe permet d'observer une posture ou un objet, **pas d'identifier un mouvement dans le temps**.
- **Lieux** : un lieu cité dans la transcription n'est pas nécessairement visible dans la vidéo.
- **Sources** : les liens fournis par l'utilisateur ne sont pas vérifiés automatiquement ; l'IA ne doit pas inventer de liens. Aucun fait n'est certifié par défaut.
- **Analyse** : les bulles IA peuvent être erronées. Le mode démo est fictif et explicitement distinct du mode IA.
- **Vie privée** : la vidéo reste locale pour le lecteur ; le fichier complet est transmis au serveur local pour Whisper, et les textes + une image fixe facultative sont transmis au fournisseur IA lors d'une analyse. Ne pas utiliser de contenu confidentiel sans autorisation.
- **Taille** : 60 Mo pour la transcription ; analyse limitée à 80 segments par appel. Pour un long documentaire, segmenter le travail.

## Architecture

HTML/CSS/JS + FastAPI + faster-whisper local + API multimodale optionnelle. Pas de base de données ni de système de comptes. La démo fonctionne sans serveur, mais le traitement IA nécessite un serveur Python et une clé API.


## Nouveautés v0.2 — mémoire culturelle et questions libres

- Chaque bulle peut être mémorisée dans le navigateur via **☆ Mémoriser**.
- La case « Ne plus proposer les découvertes déjà enregistrées » masque ces sujets lors du visionnage.
- Les sujets mémorisés sont également transmis au moteur d'analyse afin de réduire les explications répétées. La déduplication locale s'appuie sur le type et le titre normalisé : les synonymes ne sont pas toujours détectés.
- La mémoire est stockée dans `localStorage` sur cet appareil, peut être exportée et effacée. Elle n'est pas synchronisée entre appareils.
- Le bouton **Demander à Étheria** utilise l'API `/ask` avec les dialogues proches de la scène, l'image actuelle si disponible, les sources fournies et les découvertes mémorisées.
- En mode démo, les réponses sont préécrites et signalées comme telles. En mode réel, il faut `OPENAI_API_KEY`.
- La transcription Whisper reste en langue originale, sans traduction.

**Confidentialité :** les découvertes mémorisées sont envoyées au fournisseur IA lorsque l'analyse ou une question est lancée ; ne mémorisez pas d'informations sensibles.
