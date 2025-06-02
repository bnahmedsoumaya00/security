
### **6. Présentation de Wireshark**  
**Wireshark** est un **analyseur de protocoles réseau open source** utilisé pour capturer, inspecter et analyser le trafic réseau en temps réel. Il permet de diagnostiquer les problèmes réseau, détecter les vulnérabilités, et comprendre le comportement des protocoles (TCP/IP, HTTP, DNS, etc.).

---

### **2. Fonctionnalités clés de Wireshark**  

#### **A. Capture de paquets**  
- **Objectif** : Enregistrer tout le trafic réseau traversant une interface (Ethernet, Wi-Fi, loopback).  
- **Techniques** :  
  - **Mode promiscuous** : Capture tous les paquets, même ceux non destinés à la machine.  
  - **Filtres de capture** : Limiter la capture à des protocoles ou adresses spécifiques.  
    - Exemple : `tcp port 80` pour capturer uniquement le trafic HTTP.  
- **Exemple** :  
  - Analyser les requêtes DNS en temps réel via `dns` comme filtre de capture.  

#### **B. Analyse détaillée des paquets**  
- **Objectif** : Examiner les en-têtes et données de chaque paquet pour identifier des anomalies ou des vulnérabilités.  
- **Techniques** :  
  - **Décodage des protocoles** : Visualiser les couches OSI (Ethernet, IP, TCP, HTTP).  
  - **Suivi de flux TCP** : Reconstituer des échanges complets entre client et serveur.  
  - **Recherche de données sensibles** : Identifier des mots de passe en clair via `http.request.method == "POST"`.  
- **Exemple** :  
  - Détecter une requête SQLi malveillante dans le corps d’une requête HTTP.  

#### **C. Filtres avancés**  
- **Filtres de capture** : Appliqués avant la capture pour réduire le volume de données.  
  - Exemple : `src host 192.168.1.100` pour capturer uniquement le trafic d’une machine.  
- **Filtres d’affichage** : Filtrer les paquets déjà capturés.  
  - Exemple : `tcp.flags.syn == 1` pour isoler les requêtes SYN (scan de ports).  

#### **D. Statistiques et graphiques**  
- **Objectif** : Visualiser des tendances ou des comportements suspects dans le trafic.  
- **Techniques** :  
  - **Graphique de débit** : Identifier des pics de trafic pouvant indiquer une attaque DDoS.  
  - **Statistiques des protocoles** : Mesurer la proportion de trafic HTTP vs. HTTPS.  
  - **Table des conversations** : Surveiller les connexions suspectes (ex. : IP inconnue).  

---

### **3. Techniques avancées avec Wireshark**  

#### **A. Détection d’attaques réseau**  
1. **Scan de ports** :  
   - Identifier des requêtes SYN multiples vers des ports différents.  
   - Filtre : `tcp.flags.syn == 1 && tcp.flags.ack == 0`.  
2. **Attaques par force brute** :  
   - Détecter des tentatives répétées de connexion SSH via des codes d’erreur SSH.  
   - Filtre : `ssh && ssh.proto != "SSH-2.0"` pour repérer des versions obsolètes.  
3. **Vols de données** :  
   - Surveiller les transferts de fichiers via FTP/Telnet (non chiffrés).  
   - Filtre : `ftp-data` pour capturer les données transférées.  

#### **B. Analyse de vulnérabilités**  
1. **Détection de requêtes malveillantes** :  
   - Requêtes SQLi dans le corps d’une requête HTTP.  
   - Filtre : `http contains "UNION SELECT"`.  
2. **Sessions non sécurisées** :  
   - Identifier des connexions HTTP sans chiffrement.  
   - Filtre : `http && !ssl`.  

#### **C. Dépannage réseau**  
1. **Latence élevée** :  
   - Mesurer les délais de réponse TCP via `tcp.analysis.retransmission`.  
2. **Pertes de paquets** :  
   - Détecter des retransmissions fréquentes via `tcp.analysis.lost_segment`.  

---

### **4. Bonnes pratiques et conseils pour l’examen**  
1. **Maîtriser les filtres** :  
   - **Capture** : `tcp port 53` (DNS), `udp port 67` (DHCP).  
   - **Affichage** : `ip.addr == 192.168.1.1`, `http.request.method == "POST"`.  
2. **Relier aux vulnérabilités** :  
   - **XSS/SQLi** : Utiliser `http` comme filtre pour inspecter les requêtes.  
   - **Attaques réseau** : Filtrer les scans de ports via `tcp.flags.syn == 1`.  
3. **Pratiquer avec des captures** :  
   - Télécharger des captures PCAP sur [Wireshark Sample Captures](https://www.wireshark.org/#samplecaptures) pour simuler des scénarios.  
4. **Comprendre les protocoles** :  
   - Identifier les en-têtes critiques (ex. : `User-Agent`, `Set-Cookie`) pour détecter des vulnérabilités.  


---

### **Conclusion**  
Wireshark est un outil essentiel pour analyser le trafic réseau et détecter des vulnérabilités ou des attaques. Maîtrisez ses fonctionnalités clés (capture, filtres, statistiques) et pratiquez des scénarios simulés pour bien comprendre les protocoles et les menaces. Reliez ces techniques aux recommandations de sécurité (ex. : chiffrement HTTPS, détection d’anomalies) pour une préparation complète à l’examen.
  
N’hésitez pas à tester ces techniques avec des captures PCAP réalistes (ex. : CTFs, labs).
