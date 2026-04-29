# Rapport d'analyse statique - FireInTheHole

## Informations générales
- **Date d'analyse :** 29/04/2026
- **Analyste :** Ella Vanessa MUGISHA
- **APK analysé :** FireInTheHole.apk (SHA-256: 7EF29FE545103A3862EE8CA0906DE030C0CF8C12B1007ECAEFA380839756A5D8)
- **Version :** Non spécifiée
- **Outils utilisés :** MobSF v4.5.0 dans environnement Mobexler
- **Score de sécurité MobSF :** 50/100

---

## Résumé exécutif
L’analyse statique de l’application FireInTheHole révèle un **niveau de risque élevé**.  
Plusieurs vulnérabilités critiques ont été identifiées, notamment des problèmes liés à la **sécurité des communications réseau**, à la **cryptographie**, et à l’**exposition de données sensibles**.  

La base de données Firebase est accessible sans authentification, et des mécanismes cryptographiques faibles sont utilisés. Ces failles peuvent permettre l’interception, la modification ou la fuite des données sensibles.  

Ces vulnérabilités nécessitent une correction prioritaire afin de garantir la sécurité de l’application.

---

## Vulnérabilités critiques

### 1. Firebase Database Exposed
- **Sévérité :** Critique
- **Catégorie MASVS :** MSTG-NETWORK-1
- **Description :** La base de données Firebase est accessible publiquement sans authentification.
- **Preuve :** URL accessible : https://fire-in-the-hole-92c34-default-rtdb.firebaseio.com/.json
- **Impact :** Accès non autorisé aux données, fuite complète d'informations.
- **Remédiation :** Configurer des règles de sécurité Firebase et exiger une authentification.

---

### 2. Insecure SSL Implementation
- **Sévérité :** Élevée
- **Catégorie MASVS :** MSTG-NETWORK-2
- **Description :** L’application ne valide pas correctement les certificats SSL.
- **Preuve :** Implémentation SSL détectée dans le code (`c.java`)
- **Impact :** Attaque Man-In-The-Middle (MITM), interception et modification des données.
- **Remédiation :** Implémenter une validation stricte des certificats SSL.

---

### 3. Weak Cryptography (AES/CBC)
- **Sévérité :** Élevée
- **Catégorie MASVS :** MSTG-CRYPTO-3
- **Description :** Utilisation de AES/CBC avec PKCS5Padding sans intégrité.
- **Preuve :** Classe `Decrypter.java`
- **Impact :** Attaque de type padding oracle, compromission des données.
- **Remédiation :** Utiliser AES-GCM ou un chiffrement authentifié.

---

### 4. SQL Injection Risk
- **Sévérité :** Moyenne
- **Catégorie MASVS :** MSTG-PLATFORM-1
- **Description :** Certaines requêtes SQL utilisent la concaténation de chaînes.
- **Preuve :** Code SQLite analysé (`n.java`)
- **Impact :** Accès non autorisé à la base de données.
- **Remédiation :** Utiliser des requêtes paramétrées.

---

### 5. Weak Hash Algorithm (SHA-1)
- **Sévérité :** Moyenne
- **Catégorie MASVS :** MSTG-CRYPTO-1
- **Description :** Utilisation de SHA-1 pour le hachage.
- **Preuve :** Code Java analysé
- **Impact :** Risque de collision et falsification des données.
- **Remédiation :** Utiliser SHA-256 ou supérieur.

---

## Autres observations

- Utilisation de `java.util.Random` (non sécurisé)
- Présence de données potentiellement sensibles hardcodées (119 éléments détectés, majoritairement faux positifs)
- Utilisation de logs pouvant contenir des informations sensibles
- Absence de configuration de sécurité réseau explicite
- Détection de root implémentée (bonne pratique)

---

## Recommandations prioritaires

1. Sécuriser la base Firebase avec authentification et règles d’accès strictes  
2. Corriger la validation SSL pour empêcher les attaques MITM  
3. Remplacer AES/CBC par AES-GCM  
4. Supprimer toute donnée sensible stockée en clair  
5. Utiliser des algorithmes cryptographiques modernes (SHA-256, SecureRandom)

---

## Annexes

### Permissions
- INTERNET  
- ACCESS_NETWORK_STATE  

### Endpoints identifiés
- https://fire-in-the-hole-92c34-default-rtdb.firebaseio.com  
- https://accounts.google.com  
- https://console.firebase.google.com  

### Ressources sensibles
- Fichier `one.txt` utilisé pour le déchiffrement  
- Clé et IV récupérés depuis Firebase  

---

## Conclusion

L’application présente plusieurs vulnérabilités critiques qui compromettent sa sécurité globale, notamment au niveau du réseau et de la gestion des données.  

Bien que certaines bonnes pratiques soient présentes (comme la détection de root), elles ne compensent pas les faiblesses majeures identifiées.  

Une correction rapide des vulnérabilités critiques est fortement recommandée afin de réduire les risques de compromission.
