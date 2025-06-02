### **2. Google Dorks**  
#### **Objectif** :  
Utiliser des opérateurs de recherche avancés sur Google pour identifier des vulnérabilités ou des informations sensibles (ex. : fichiers exposés, serveurs mal configurés).

---

#### **Opérateurs clés** :  
| Opérateur          | Utilisation                                      | Exemple                          |
|---------------------|--------------------------------------------------|----------------------------------|
| `intitle:`          | Recherche par titre de page                      | `intitle:"index of" site:tek-up.de` |
| `inurl:`            | Recherche dans l’URL                             | `inurl:admin filetype:php`       |
| `filetype:`         | Fichiers d’un type spécifique                    | `filetype:sql "password"`        |
| `site:`             | Limiter les résultats à un domaine               | `site:gov.tn confidential`       |
| `cache:`            | Afficher la version mise en cache d’une page     | `cache:google.com`               |
| `related:`          | Trouver des sites similaires                     | `related:nytimes.com`            |
| `link:`             | Trouver des pages liées à un site                | `link:owasp.org`                 |

---

#### **Exemples pratiques** :  
1. **Recherche de fichiers sensibles** :  
   - Fichiers SQL contenant des données :  
     ```dork
     filetype:sql "CREATE TABLE users" site:entreprise.com
     ```  
   - Backups de configurations :  
     ```dork
     filetype:bkf OR filetype:backup "config" site:banque.tn
     ```  

2. **Identification de serveurs vulnérables** :  
   - Serveurs IIS Windows :  
     ```dork
     intitle:iisstart.htm intitle:iis7
     ```  
   - Serveurs Apache 2.2.34 :  
     ```dork
     server:Apache/2.2.34
     ```  

3. **Formulaires de connexion** :  
   ```dork
   inurl:login OR inurl:auth OR inurl:signin filetype:php
   ```  

4. **Serveurs SMTP ou FTP** :  
   ```dork
   intitle:"SMTP Server Status" OR intitle:"FTP Server"
   ```  

5. **Caméras de surveillance** :  
   ```dork
   intitle:"live view" intitle:axis
   ```  

---

#### **Cas réels** :  
- **Vol de mots de passe** :  
  Recherche de fichiers `.env` ou `.git` exposés :  
  ```dork
  filetype:env "DB_PASSWORD" site:startup.tn
  ```  
- **Serveurs mal sécurisés** :  
  Accès à des dashboards d’administration via :  
  ```dork
  inurl:/dashboard/admin intitle:"Admin Panel"
  ```  

---

#### **Outils spécialisés** :  
1. **Google Hacking Database (GHDB)** :  
   - Base de données de requêtes prédéfinies pour cibler des vulnérabilités spécifiques.  
   - Exemple : Recherche de formulaires d’upload :  
     ```dork
     inurl:upload filetype:php site:*.edu
     ```  

2. **FOCA (Fingerprinting Organizations with Collected Archives)** :  
   - Analyse des fichiers trouvés via Google Dorks pour extraire des métadonnées (ex. : noms d’utilisateurs, chemins réseau).  

3. **TheHarvester** :  
   - Collecte d’emails et de sous-domaines à partir de requêtes Google Dorks.  

---

#### **Prévention** :  
1. **Robots.txt** :  
   - Bloquer l’indexation des pages sensibles :  
     ```txt
     User-agent: *
     Disallow: /admin/
     Disallow: /config/
     ```  

2. **Gestion des erreurs** :  
   - Désactiver l’affichage des erreurs détaillées (ex. : logs, chemins).  

3. **Sécurité des fichiers** :  
   - Protéger les backups et configurations via des permissions strictes (ex. : `.htaccess` en Apache).  

4. **Audit régulier** :  
   - Utiliser **Google Search Console** pour surveiller l’indexation de votre site.  

---
