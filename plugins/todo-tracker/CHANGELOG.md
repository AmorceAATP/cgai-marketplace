# Changelog — todo-tracker

## 0.2.0
- **Routage TODO départemental/personnel** : avant chaque proposition, la
  destination est déterminée. Question explicite à l'employé en cas
  d'ambiguïté (le skill ne devine plus). Destination tranchée + affichée +
  confirmée au moment de l'écriture (champ `DESTINATION:` dans le bloc HITL).
- **Garde-fou anti-fuite CONFIDENTIAL** : un item contenant des données
  sensibles (paye, TSX, données personnelles) ne peut jamais aller au TODO
  départemental — redirigé vers le personnel et signalé.

## 0.1.0
- Version initiale (marketplace cgai-amorce).
