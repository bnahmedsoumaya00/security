



---



### **1. Présentation de Burp Suite**  
**Burp Suite** est un outil de test de sécurité des applications web, largement utilisé pour détecter et exploiter des vulnérabilités (XSS, SQLi, CSRF, etc.). Il agit comme un proxy entre le navigateur et le serveur, permettant d’intercepter, modifier et rejouer les requêtes HTTP.

---

### **2. Fonctionnalités clés de Burp Suite**  

#### **A. Intruder**  
- **Objectif** : Exécution automatisée d’attaques par force brute ou injection (ex. : SQLi, XSS, fuzzing).  
- **Techniques** :  
  - **Payloads** : Envoi de listes de valeurs (ex. : mots de passe, payloads SQL) via des positions spécifiques dans une requête.  
  - **Attack Types** :  
    - **Sniper** : Test de chaque payload une fois par position.  
    - **Cluster bomb** : Combinaison de plusieurs payloads pour tester des scénarios complexes.  
    - **Pitchfork** : Association de payloads en paires (ex. : login/mot de passe).  
- **Exemple** :  
  - Exploitation d’une faille SQLi en envoyant des payloads comme `' OR 1=1 --` via `Intruder > Payloads`.  

#### **B. Repeater**  
- **Objectif** : Modification manuelle et réexécution de requêtes HTTP pour tester des vulnérabilités.  
- **Techniques** :  
  - **Interception** : Capture et modification de requêtes via `Proxy > Intercept`.  
  - **Tests de vulnérabilités** :  
    - **XSS** : Injection de scripts comme `<script>alert(1)</script>` dans des paramètres.  
    - **CSRF** : Analyse de formulaires sans jeton CSRF.  
- **Exemple** :  
  - Test d’une injection SQL en modifiant un paramètre `id=1` en `id=1' UNION SELECT username, password FROM users --`.  

#### **C. Sequencer**  
- **Objectif** : Analyse de l’entropie et de la prévisibilité des jetons de session (ex. : cookies, tokens CSRF).  
- **Techniques** :  
  - **Collecte de données** : Capture de multiples jetons pour évaluer leur aléa.  
  - **Rapport de sécurité** : Mesure de la qualité du chiffrement (ex. : tokens faibles exploitables via fixation de session).  
- **Exemple** :  
  - Détection de jetons de session prévisibles (ex. : `sessionid=123456789`).  

#### **D. Decoder**  
- **Objectif** : Encodage/décodage de données (ex. : URL, HTML, Base64, Hex).  
- **Techniques** :  
  - **Manipulation de payloads** : Encodage de scripts malveillants en Base64 pour bypasser les filtres.  
  - **Détection de contenu malveillant** : Décodage de requêtes encodées pour révéler des attaques cachées.  
- **Exemple** :  
  - Décodage d’un payload en Base64 : `PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==` → `<script>alert(1)</script>`.  

#### **E. Extender**  
- **Objectif** : Intégration de modules complémentaires (plugins) pour étendre les fonctionnalités de Burp Suite.  
- **Plugins populaires** :  
  - **Logger++** : Journalisation automatique des requêtes.  
  - **Autorize** : Test de contournement des contrôles d’accès.  
  - **SQLMap** : Automatisation des tests SQLi.  
- **Exemple** :  
  - Utilisation de **Logger++** pour surveiller les requêtes sensibles (ex. : formulaires de login).  

---

### **3. Techniques avancées avec Burp Suite**  

#### **A. Interception et modification de requêtes**  
1. **Configuration du proxy** :  
   - Paramétrer le navigateur pour utiliser `127.0.0.1:8080` comme proxy.  
2. **Interception** :  
   - Activer `Proxy > Intercept on` pour capturer les requêtes.  
3. **Modification** :  
   - Modifier des paramètres GET/POST, des headers HTTP (ex. : `User-Agent`, `Referer`).  

#### **B. Test de vulnérabilités XSS**  
1. **Injection de payloads** :  
   - Utiliser `Repeater` pour tester des scripts comme `<img src=x onerror=alert(1)>`.  
2. **Détection de filtres** :  
   - Tester des payloads encodés (ex. : `<scriPt>alert(1)</scRipt>`) pour bypasser les WAF.  

#### **C. Exploitation de SQLi**  
1. **Identification de vulnérabilités** :  
   - Envoyer une requête à `Intruder` avec des payloads comme `' OR 1=1 --`.  
2. **Extraction de données** :  
   - Utiliser `UNION SELECT` pour récupérer des informations (ex. : `username`, `password`).  

#### **D. Analyse de jetons de session**  
1. **Collecte de tokens** :  
   - Utiliser `Sequencer` pour capturer des cookies de session.  
2. **Évaluation de la sécurité** :  
   - Vérifier si les tokens sont aléatoires ou prévisibles (ex. : `sessionid=1234`).  

---


