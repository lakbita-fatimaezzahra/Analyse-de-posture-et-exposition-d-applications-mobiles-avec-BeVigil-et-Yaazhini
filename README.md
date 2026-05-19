# Analyse-de-posture-et-exposition-d-applications-mobiles-avec-BeVigil-et-Yaazhini

#  Lab: Audit Défensif d'une Application Mobile (`DivaApplication`)

##  Description du Projet
Ce laboratoire est dédié à la mise en place de l'environnement, à la définition du périmètre (*scoping*) et à l'analyse de surface d'une application Android (`.apk`). À travers ce lab, nous mettons en œuvre une méthodologie rigoureuse d'audit défensif en documentant chaque étape, de la vérification de l'intégrité de la cible jusqu'à la collecte des signaux d'exposition.

---

##  Objectifs Pédagogiques
À la fin de ce lab, l'apprenant sera capable de :
*  **Organiser** un audit défensif d'application mobile (arborescence standardisée, traçabilité des commandes).
*  **Collecter** des signaux d'exposition avec **BeVigil** et **Yaazhini**.
*  **Trier** les résultats et identifier les faux positifs.
*  **Corréler** les constats avec les standards internationaux **OWASP MASVS / MASTG**.
*  **Produire** un rapport d'analyse technique et managérial exploitable par les équipes de développement.

---

## 1. Initialisation de l'Environnement de Travail

Pour garantir la rigueur de l'audit et la reproductibilité des étapes, l'arborescence du projet a été initialisée sous Windows PowerShell avec une structure sectorisée :

```powershell
# Création du dossier principal et de ses sous-dossiers
mkdir lab-mobile-security
cd lab-mobile-security
mkdir 00-scope, 01-bevigil, 02-yaazhini, 03-triage, 04-report

<img width="553" height="776" alt="Screenshot 2026-05-19 143038" src="https://github.com/user-attachments/assets/57456195-1003-4564-85f2-826f3c46d77d" />

## Rôle des Dossiers

*00-scope/ : Contient l'artefact cible (l'APK d'origine) et la liste des domaines autorisés (Targeting).

*01-bevigil/ : Recherches OSINT et signaux d'exposition récupérés via les outils BeVigil.

*02-yaazhini/ : Rapports bruts d'analyse statique automatisée (SAST) générés par Yaazhini.

*03-triage/ : Analyse critique des alertes, qualification des vulnérabilités et élimination des faux positifs.

*04-report/ : Livrable final contenant les vulnérabilités validées et corrélées aux référentiels de sécurité.

**## 2. Intégrité de la Cible & Fiche d'Analyse (00-scope)**
 Importation et Calcul de l'Empreinte (SHA-256):
L'application cible DivaApplication.apk a été déplacée dans le dossier de scope. Afin de garantir qu'aucune altération ou corruption n'ait lieu durant l'analyse, son

empreinte numérique unique a été calculée :

<img width="1712" height="219" alt="Screenshot 2026-05-19 144010" src="https://github.com/user-attachments/assets/513b7a9b-049c-45e4-aee2-af80b5f9de7a" />

**Automatisation de la Fiche d'Information**

Le hash calculé a été injecté dynamiquement dans la fiche de suivi globale du laboratoire analyse_info.txt en remplaçant la balise temporaire :

<img width="1712" height="219" alt="Screenshot 2026-05-19 144010" src="https://github.com/user-attachments/assets/ab86a67c-3983-4828-9438-e212ee5257c4" />

Contenu vérifié du fichier analyse_info.txt :

<img width="1701" height="310" alt="Screenshot 2026-05-19 144745" src="https://github.com/user-attachments/assets/b01eacb1-2b69-4095-af62-1bc2affde12a" />

**##3. Définition du Périmètre Réseau (Targeting)**

Afin de baliser strictement le cadre légal et technique de l'audit et éviter tout test hors-périmètre (Out of Scope), les domaines officiels rattachés à l'application ont été consignés dans le fichier 00-scope\\targets.txt à l'aide d'une chaîne multiligne (Here-String) :

<img width="905" height="153" alt="Screenshot 2026-05-19 145004" src="https://github.com/user-attachments/assets/f3d57469-8137-4735-9476-8d827430a838" />

<img width="884" height="330" alt="Screenshot 2026-05-19 144955" src="https://github.com/user-attachments/assets/be04a15b-469c-40f7-b187-fd20a161862b" />


**##4. Traçabilité et Auditabilité (commands.log)**

Dans une démarche professionnelle, chaque action, outil utilisé et commande clé doit être tracé pour l'historique d'audit. Les commandes et actions clés sont consignées au fur et à mesure dans le fichier commands.log :

<img width="1615" height="60" alt="Screenshot 2026-05-19 145156" src="https://github.com/user-attachments/assets/31619d50-1b38-4ae5-8a84-6254937c1035" />

<img width="889" height="253" alt="Screenshot 2026-05-19 145134" src="https://github.com/user-attachments/assets/2aa0e505-cab8-407c-9825-e3372d40f7b4" />



**##4. Phase 01: Collecte OSINT & Threat Intelligence (BeVigil)**

L'artefact a été soumis à la plateforme de Threat Intelligence BeVigil (CloudSEK) pour analyser les métadonnées indexées et déceler d'éventuelles fuites d'informations publiques sur le package jakhar.aseem.diva (v1.0).

<img width="1444" height="245" alt="image" src="https://github.com/user-attachments/assets/879bc3b2-76dd-4974-83e3-10edff823810" />

<img width="1917" height="649" alt="image" src="https://github.com/user-attachments/assets/69881eb1-30f8-4aa1-a454-0f226b1284d2" />

<img width="1919" height="519" alt="image" src="https://github.com/user-attachments/assets/193a5e50-abd0-4232-900b-7c97e03dbd8d" />

<img width="1908" height="749" alt="image" src="https://github.com/user-attachments/assets/9a22d044-2afb-492e-9924-35cbc2418241" />

#  Task 4 — Collecte BeVigil : Endpoints, Domaines, Emails & Technologies

##  Description de la Tâche
Cette étape consiste à explorer méthodiquement les résultats fournis par la plateforme de Threat Intelligence **BeVigil (CloudSEK)** sur l'application cible (`jakhar.aseem.diva`). L'objectif est de cartographier la surface d'attaque externe en extrayant les domaines, endpoints, chaînes sensibles et technologies afin de préparer les phases de triage et d'exploitation.

---

##  Commandes Windows (PowerShell) Exécutées

# 1. Création du fichier bevigil_notes.md avec la structure initiale requise

<img width="1714" height="769" alt="image" src="https://github.com/user-attachments/assets/d6001598-d644-4351-bd3d-7990a92c2d08" />
<img width="1293" height="911" alt="image" src="https://github.com/user-attachments/assets/a54df3e2-205f-4d18-80b8-2c4ab0ab20e4" />


# 2. Journalisation de l'action dans le fichier de traçabilité

<img width="1531" height="57" alt="image" src="https://github.com/user-attachments/assets/e9fb3bed-230f-4ac7-b0db-714e0fc9b036" />

<img width="1115" height="382" alt="image" src="https://github.com/user-attachments/assets/7dfcc167-66a1-4a26-a015-d746d02b6c05" />

#  Task 5 — Démarrage et prise en main de Yaazhini

##  Description de la Tâche
Cette étape marque le lancement de l'analyse statique automatisée (SAST) locale à l'aide de l'outil **Yaazhini**. Contrairement à l'approche de Threat Intelligence externe (BeVigil), Yaazhini décompile l'artefact `.apk` localement afin de disséquer le code source, d'inspecter le fichier de configuration `AndroidManifest.xml` et de cartographier de manière exhaustive les vulnérabilités structurelles cachées à l'intérieur des ressources de l'application.

##  Commandes Windows (PowerShell) Exécutées

Selon l'architecture de l'outil mis à disposition dans votre environnement de laboratoire, l'une des deux approches d'automatisation suivantes a été exécutée.

### Option A : Version Exécutable Windows (`yaazhini.exe`)

# 1. Création d'un espace de travail temporaire sécurisé

<img width="1041" height="261" alt="image" src="https://github.com/user-attachments/assets/e1f905b0-3ace-4020-88ac-86dc18180de5" />

# 2. Exécution du scanner SAST sur l'APK cible du scope

<img width="1603" height="68" alt="image" src="https://github.com/user-attachments/assets/7118a454-b7d6-408f-815d-20b0776b36e3" />
<img width="1344" height="888" alt="image" src="https://github.com/user-attachments/assets/7732c437-c97c-41f0-99eb-f7ef6404ae3c" />
<img width="999" height="784" alt="image" src="https://github.com/user-attachments/assets/a2a5bc35-ef13-4b6a-b08d-2dec835ea871" />

Les vulnérabilités trouvées:

<img width="1919" height="511" alt="image" src="https://github.com/user-attachments/assets/a42f6ae6-54e2-4470-8a6d-e3d10d5209b1" />

# 3. Migration persistante du rapport complet généré vers le dossier dédié

