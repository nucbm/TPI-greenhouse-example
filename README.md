
# 🌱 Smart Greenhouse Monitoring

### Mini-aplicație IoT – Simularea monitorizării inteligente a unei sere

**Echipă:** AgroTech IoT  
**Membri:** Student 1, Student 2, Student 3  
**Repository GitHub:** `https://github.com/organizatie/smart-greenhouse-monitor` (exemplu)  
**Scenariu:** Smart Agriculture  
**Status:** Propunere / Studiu preliminar

---

## 1. Descrierea scenariului

Proiectul urmărește simularea unui sistem IoT pentru monitorizarea condițiilor de mediu dintr-o seră inteligentă.

Sistemul colectează date simulate de la senzori (temperatură, umiditate, umiditatea solului), le transmite către un gateway/edge, apoi către un serviciu cloud, unde sunt stocate, analizate și prezentate utilizatorului printr-o interfață web.

Aplicația trebuie să permită **identificarea și semnalarea unor anomalii**, de exemplu creșterea excesivă a temperaturii sau scăderea bruscă a umidității solului.

**Întrebarea principală:** Cum poate un sistem IoT să identifice automat condiții anormale într-o seră, utilizând date provenite de la senzori?

## 2. Obiectivele proiectului

### Obiective obligatorii (MVP)

- [ ] Simularea a minimum 2–3 senzori IoT
- [ ] Transmiterea periodică a datelor către un gateway
- [ ] Implementarea unei prelucrări simple la nivel edge
- [ ] Transmiterea și stocarea datelor în cloud/backend
- [ ] Dezvoltarea unei interfețe web pentru vizualizarea datelor
- [ ] Implementarea unui mecanism de identificare a anomaliilor
- [ ] Afișarea alertelor și a istoricului acestora

### Obiective opționale

- [ ] Utilizarea unui dataset public cu date reale
- [ ] Compararea a două metode de detecție a anomaliilor
- [ ] Generarea de rapoarte sau statistici
- [ ] Simularea unei acțiuni automate (de exemplu, pornirea ventilației)

### Obiectiv de excelență – AI / LLM

Integrarea unui asistent conversațional care permite utilizatorului să formuleze întrebări în limbaj natural despre datele colectate.

Exemple:

- „Care a fost temperatura maximă în ultimele 24 de ore?”
- „Au existat anomalii astăzi?”
- „În ce intervale umiditatea solului a fost critică?”
- „Explică posibilele cauze ale alertei din această dimineață.”

Răspunsurile trebuie să fie fundamentate pe datele aplicației, nu doar pe cunoștințele generale ale modelului.

## 3. Arhitectura propusă

Fluxul de date este:

**Simulator senzori → Edge/Gateway → Cloud/Backend → Frontend**

| Componentă | Responsabilitate |
|---|---|
| Sensor Simulator | Generează date sintetice, inclusiv valori anormale |
| Edge/Gateway | Primește date, validează valori, realizează preprocesare și poate detecta anomalii |
| Cloud/Backend | Expune API-uri, stochează date și gestionează alertele |
| Frontend | Prezintă datele, graficele și anomaliile |
| LLM (opțional) | Interpretează întrebări și formulează răspunsuri pe baza datelor stocate |

**Schița arhitecturii:**

```text
[Temperature Sensor] ─┐
[Humidity Sensor] ────┼──► [Edge/Gateway]
[Soil Sensor] ────────┘          │
                                │ MQTT / HTTP
                                ▼
                         [Cloud Backend]
                         ├── REST API
                         ├── Database
                         └── Anomaly Events
                                │
                                ▼
                         [Web Dashboard]
                         ├── Charts
                         ├── Sensor Status
                         └── Alerts

             [LLM Assistant - optional]
                       │
                Backend API / DB
```

Nu este necesară utilizarea unor dispozitive hardware reale. Toate componentele pot rula local, în containere sau în servicii cloud gratuite.

## 4. Datele și simularea senzorilor

### Senzori propuși

| Senzor | Unitate | Interval normal orientativ | Frecvență |
|---|---|---|---|
| Temperatură | °C | 18–30 | 5 secunde |
| Umiditate aer | % | 40–80 | 5 secunde |
| Umiditate sol | % | 30–70 | 5 secunde |

Intervalele sunt valori demonstrative; pot fi ajustate pentru cultura vegetală simulată.

### Exemplu de mesaj transmis

```json
{
  "device_id": "greenhouse-01",
  "timestamp": "2026-10-09T10:15:00Z",
  "temperature": 35.8,
  "humidity": 55.2,
  "soil_moisture": 28.4
}
```

### Generarea datelor

Se propune un simulator Python care:

1. Generează valori în intervale normale, cu variații aleatoare.
2. Simulează evoluția valorilor în timp.
3. Introduce controlat anomalii.
4. Transmite date către gateway prin MQTT sau HTTP.

**Dataset extern (opțional):** Un dataset public selectat de echipă, de exemplu de pe Kaggle sau dintr-un portal open-data.

Pentru un dataset ales se vor documenta: denumirea, URL-ul, sursa, licența, variabilele, dimensiunea, intervalul temporal și modalitatea de utilizare.

## 5. Identificarea anomaliilor

**Anomalie aleasă:** temperatura din seră depășește un prag critic.

Metoda inițială propusă este una bazată pe reguli:

```text
IF temperature > 32°C
FOR 3 consecutive readings
THEN generate HIGH_TEMPERATURE alert
```

Gateway-ul sau backend-ul va înregistra evenimentul și îl va pune la dispoziția frontend-ului.

### Tipuri de anomalii

- Depășirea unui prag critic
- Modificarea bruscă a unei valori
- Lipsa datelor de la un senzor
- Valori inconsistente sau imposibile fizic

**Extensie opțională:** detecție statistică (Z-score, medie mobilă) sau algoritmi ML (Isolation Forest).

### Evaluarea detecției

Simulatorul va marca evenimentele anormale introduse artificial, pentru a putea evalua algoritmul.

Indicatori propuși:

- Numărul de anomalii detectate
- Numărul de alarme false
- Precision / Recall, dacă există etichete de referință
- Latența dintre producerea anomaliei și afișarea alertei

## 6. Interfața frontend – schiță preliminară

Aplicația va avea un dashboard cu:

- Valorile curente ale senzorilor
- Grafice privind evoluția în timp
- Evidențierea anomaliilor
- Lista alertelor recente
- Selector pentru perioada analizată
- Panou de întrebări pentru asistentul AI (opțional)

**Wireframe orientativ:**

```text
+--------------------------------------------------+
|          SMART GREENHOUSE DASHBOARD              |
+--------------------------------------------------+
| Temperature   | Air Humidity  | Soil Moisture    |
|   35.8 °C     |    55.2 %     |    28.4 %        |
|   WARNING!    |    NORMAL     |    LOW           |
+--------------------------------------------------+
|                                                  |
|  Temperature over time                           |
|  [              LINE CHART                     ] |
|                                                  |
+--------------------------------------------------+
| Recent Alerts                                    |
| 10:15  HIGH TEMPERATURE       CRITICAL           |
| 10:10  LOW SOIL MOISTURE      WARNING            |
+--------------------------------------------------+
| Ask your greenhouse (optional)                   |
| [ Why was temperature high today?           ]    |
| [ Send ]                                         |
+--------------------------------------------------+
```

**Implementare propusă:** React + o bibliotecă de grafice (Recharts sau Chart.js).

Echipa poate atașa ulterior o schiță realizată în Figma, Excalidraw sau chiar desenată de mână.

## 7. Tehnologii și API-uri

Stack tehnologic preliminar:

| Componentă | Tehnologie propusă |
|---|---|
| Sensor Simulator | Python |
| Edge/Gateway | Python |
| Comunicare IoT | MQTT / HTTP |
| Broker MQTT | Mosquitto |
| Backend API | FastAPI |
| Database | SQLite / PostgreSQL |
| Frontend | React |
| Grafice | Recharts |
| Deployment (opțional) | Docker Compose |
| LLM (opțional) | API LLM sau model local |

Tehnologiile sunt orientative; echipa poate propune alternative, cu o justificare succintă.

### API-uri propuse

| Endpoint | Metodă | Descriere |
|---|---|---|
| `/api/sensors/latest` | GET | Returnează ultimele valori |
| `/api/measurements` | GET | Returnează istoricul măsurătorilor |
| `/api/alerts` | GET | Returnează anomaliile detectate |
| `/api/simulator/config` | POST | Configurează simulatorul și injectarea de anomalii |
| `/api/ask` | POST | Primește întrebări în limbaj natural (opțional) |

Se vor documenta ulterior exemple de request/response și principalele erori.

## 8. Integrare LLM – extensie de excelență

Utilizatorul poate întreba:

„Care au fost principalele probleme din seră în ultimele 24 de ore?”

Flux propus:

1. Frontend-ul trimite întrebarea către backend.
2. Backend-ul identifică datele relevante și interoghează baza de date.
3. Rezultatele sunt furnizate LLM-ului ca informații de context.
4. LLM-ul construiește un răspuns explicativ.
5. Frontend-ul afișează răspunsul și, unde este posibil, intervalele și măsurătorile utilizate.

Exemplu de răspuns:

„În ultimele 24 de ore au fost înregistrate trei alerte de temperatură ridicată. Temperatura maximă a fost 37,2°C, la ora 14:35.”

*Valorile sunt ilustrative.*

**Cerință importantă:** LLM-ul trebuie să utilizeze date reale din aplicație, să indice când nu există suficiente informații și să nu inventeze măsurători. Cheile API vor fi păstrate pe backend, nu în frontend.

## 9. Etape de implementare (sprinturi)

| Sprint | Activități principale | Livrabil |
|---|---|---|
| Sprint 0 | Alegerea scenariului, arhitectură, README, repository | Propunerea proiectului |
| Sprint 1 | Implementarea simulatorului de senzori | Flux de date simulate |
| Sprint 2 | Gateway, comunicare și stocare cloud/backend | Pipeline funcțional |
| Sprint 3 | Frontend, grafice și dashboard | Interfață demonstrabilă |
| Sprint 4 | Detecție de anomalii, alerte, testare | MVP complet |
| Sprint 5 (opțional) | Integrare LLM și îmbunătățiri | Funcționalități avansate |

Durata fiecărui sprint va fi adaptată calendarului disciplinei.

## 10. Repartizarea responsabilităților

| Membru | Responsabilități principale |
|---|---|
| Student 1 | Simulator senzori și edge/gateway |
| Student 2 | Backend, API și baza de date |
| Student 3 | Frontend, dashboard și integrare |

Toți membrii participă la proiectarea arhitecturii, integrare, testare și documentare. Pentru echipele de doi studenți, responsabilitățile vor fi redistribuite.

Contribuțiile vor fi vizibile prin commit-uri și pull request-uri în GitHub.

## 11. Structura preliminară a repository-ului

```text
smart-greenhouse-monitor/
├── README.md
├── simulator/
│   └── sensor_simulator.py
├── edge/
│   └── gateway.py
├── backend/
│   ├── api/
│   └── database/
├── frontend/
│   └── src/
├── datasets/
├── docs/
│   ├── architecture.png
│   └── dashboard-sketch.png
├── tests/
└── docker-compose.yml
```

Structura poate evolua pe parcursul proiectului.

## 12. Testare și criterii de succes

La finalul proiectului, echipa va demonstra următorul scenariu end-to-end:

1. Simulatorul generează date normale.
2. Datele traversează gateway-ul și ajung în backend.
3. Dashboard-ul afișează valorile și actualizează graficele.
4. Simulatorul introduce o anomalie de temperatură.
5. Sistemul identifică anomalia și generează o alertă.
6. Alerta este vizibilă în frontend, împreună cu informații despre momentul și cauza declanșării.

**Criteriu minim de succes:** existența unui flux demonstrabil senzor simulat → edge/gateway → cloud/backend → frontend, cu detectarea și afișarea cel puțin a unui tip de anomalie.

Pentru varianta de excelență, echipa demonstrează suplimentar răspunsuri generate de LLM pe baza datelor colectate.

## 13. Riscuri și limitări

- Datele simulate pot să nu reproducă fidel comportamentul senzorilor reali.
- Pragurile fixe pot genera alarme false.
- Rețeaua și serviciile pot introduce întârzieri.
- Integrarea unui LLM poate implica latență și costuri.
- Timpul disponibil poate impune simplificarea unor componente.

**Strategie de reducere a riscurilor:** prioritizarea MVP-ului și implementarea funcționalităților opționale numai după validarea fluxului end-to-end.

## 14. Referințe și resurse

Echipa va completa această secțiune cu:

- Documentația tehnologiilor utilizate
- Linkuri către dataset-uri
- Specificații ale API-urilor externe
- Articole sau tutoriale relevante
- Surse pentru algoritmii de detectare a anomaliilor

## 15. Întrebări deschise

- Unde este mai potrivită detecția anomaliilor: în edge sau în cloud?
- Ce protocol de comunicare se potrivește scenariului ales?
- Ce date trebuie păstrate și pentru cât timp?
- Cum va fi măsurată performanța mecanismului de detecție?
- Ce funcționalități pot fi eliminate dacă timpul este limitat?

Aceste întrebări vor fi clarificate pe parcursul implementării.

---

**Notă:** Acest README constituie propunerea inițială a proiectului și va fi actualizat pe măsură ce arhitectura, tehnologiile și funcționalitățile sunt validate.
