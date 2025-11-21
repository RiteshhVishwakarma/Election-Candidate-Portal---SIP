Classroom-Based SIP Project Report: Election Candidate Portal

1. Project Overview
 * Project Title: Election Candidate Portal (Client-Side CRUD & Voting Simulation)
 * Project Type: Frontend
 * Short Description: A responsive, single-page web application designed to simulate an election portal. It allows users to browse, search, filter, add, and edit candidate profiles. It includes a vote simulation feature and visualizes vote totals using a simple bar chart. Data persistence is managed entirely client-side using localStorage.
 * Purpose: The project serves as a functional demonstration of C.R.U.D. operations (Create, Read, Update, Delete - although Delete is omitted, Edit is included) using Vanilla JavaScript, complex DOM manipulation, client-side data persistence, and responsive UI design, all without a dedicated backend server.
 * Problem Statement: The need to create a fast, interactive, and self-contained data management application for candidate tracking and voting simulation, relying solely on frontend technologies to showcase dynamic data handling and presentation.

2. Project Objectives
 * Development Objectives: To successfully build a client-side C.R.U.D. application that manages a list of candidate objects, including handling form submissions for creation/editing and updating vote counts.
 * Technical Objectives: To master localStorage for structured data persistence, implement efficient rendering logic (re-render entire list upon data change), handle complex event delegation for dynamic card buttons (Vote/Edit), and implement data filtering/searching.
 * Learning Outcomes: Proficiency in building practical single-page applications, advanced skills in dynamic HTML generation (.innerHTML), data serialization (JSON), and creating simple data visualizations (bar charts) using CSS/JS.
 * Key Features: Candidate Listing (Read), Candidate Form (Create/Update), Client-Side Search and Filter, Vote Simulation, Real-time Statistics, and Vote Bar Chart Visualization.

3. Project Scope
 * Modules / Pages Included: A single main page (index.html) featuring the Candidate List section and the Candidate Form/Stats Sidebar.
 * Functional Coverage: Adding new candidates, editing existing candidates, filtering by party, searching by name/party/bio, simulating votes, displaying total stats, and data persistence across sessions.
 * Technology Stack to Be Used: HTML5, CSS3 (with flexbox/grid), and Vanilla JavaScript.
 * Database / Backend Scope (if any): None. Data is stored as a JSON string in localStorage.
 * Exclusions / Limitations: No real-time multi-user synchronization, no actual backend database, no user authentication, and no robust input validation (basic name check only).

4. Technology Stack
List tools clearly:
Frontend
 * HTML: Defines the two main sections (<section> for list, <aside> for form/stats) and all control elements.
 * CSS: Uses Grid for the main page layout (main), Flexbox for internal components (cards, headers), and media queries for responsiveness (collapsing the two-column layout on mobile).
 * JavaScript: Vanilla JavaScript handles all C.R.U.D. logic, state management (state array), localStorage read/write operations, and all UI rendering/event handling.
Backend
 * None.
Database
 * None. (Data is persisted in the browser's localStorage).
Development Tools
 * VS Code: Primary code editor.
 * Browser Developer Tools: Used extensively for debugging JavaScript logic, inspecting localStorage content, and refining CSS Grid/Flexbox layouts.

5. Requirement Analysis
Functional Requirements
 * The system must display a list of all candidates with their name, party, bio, and vote count.
 * Users must be able to add a new candidate via the form.
 * Users must be able to edit an existing candidate by clicking the "Edit" button on the card.
 * Clicking the "Vote" button must increment the candidate's vote count by one.
 * Data (candidates and votes) must be saved and loaded from localStorage.
 * The candidate list must update automatically upon Save or Vote actions.
 * The system must allow filtering candidates by party (All, Independent, Party A, Party B).
 * The system must allow searching candidates by name, party, or bio.

Non-Functional Requirements
 * Usability: The interface must be clearly divided (List vs. Form) and the application must be intuitive.
 * Responsiveness: The layout must transition smoothly from two columns on desktop to a single column on mobile.
 * Performance: Filtering and rendering operations must be fast, even with a moderate number of candidates.
 * Data Integrity: Basic HTML escaping (escapeHtml) is used to prevent simple XSS attacks when displaying user-inputted data.

User Roles
 * Admin/Simulator (Sole Role): The user acts as both the data administrator (adding/editing candidates) and the vote simulator (casting votes).

Input / Output Requirements
 * Inputs expected from users:
   * Form Fields: Name, Party, Age, Bio (for C/U operations).
   * Search: Text input (#q).
   * Actions: Clicks on Vote/Edit buttons, Save/Clear buttons, and Party Chips.
 * Outputs generated by system:
   * Dynamic list of candidate cards based on search/filter.
   * Real-time Total Candidates and Total Votes statistics.
   * Dynamic Vote Bar Chart reflecting current vote distribution.
   * Form fields pre-filled during an Edit action.

6. System Analysis

Problem Statement
The core engineering problem is implementing a state management pattern where every user interaction (save, vote, filter, search) triggers a controlled re-render cycle (save() -> render()) to ensure the entire UI (list, stats, chart) remains synchronized with the client-side state array.

Proposed System
The system is built around a global JavaScript array, state, which holds all candidate objects.
 * Persistence Layer: load() and save() functions serialize the state array to/from localStorage.
 * Controller Functions: onSave(e) handles form submission for both creation (no editingId) and updating (with editingId). voteFor(id) increments the vote count.
 * View Layer: render() reads the current state, applies filters/search, dynamically generates all candidate cards, updates the stat boxes, and calls renderChart().

Justification
This approach minimizes reliance on external APIs and is highly efficient for prototyping, as the entire application logic is contained in one portable file, demonstrating mastery of Vanilla JS application design.

7. System Design
Layout Design
The layout follows a standard dashboard pattern: a full-width header and a two-column main section (List on the left, Form/Stats on the right) for wide screens.
Architecture
 * Frontend-only Architecture (State-Driven Rendering):
   * Data Store: state array (in memory) synced with localStorage (persistent).
   * Logic: Pure JavaScript functions modify the state array.
   * View: The render() function serves as the single source of truth for updating the entire user interface.

UI/UX Plan
 * Color scheme: Uses a dark, professional theme (Deep Blue/Black background, --bg: #0f1724) accented by a bright gold/yellow (--accent: #F8B500) for primary buttons and chart elements, promoting a professional feel.
 * Design principles used: Information Grouping (Stats clearly grouped at the top of the sidebar), Direct Manipulation (Voting/Editing performed directly from the candidate cards), and Responsiveness (Adapting the grid-template-columns based on screen size).

8. Implementation Details
Module Development
 * Data Management: The save() and load() functions handle serialization (JSON.stringify) and deserialization (JSON.parse) to manage data persistence.
 * Card Generation: The render() function iterates through the filtered candidates and builds the HTML for each card using template literals (`el.innerHTML = ``) for efficient DOM insertion.
 * Filtering: The party chips use a data-party attribute to activate the filter, and the render() function dynamically filters the state based on the active chip/search query.
 * Chart Generation: renderChart() calculates the maximum vote count to normalize the bar heights and then sets the height style of the bar elements proportionally.
Important Code Snippets
// Function to handle both Creation and Update (C/U)
function onSave(e){
  // ... input reading and validation

  if(editingId){ // If editing an existing candidate
    const idx = state.findIndex(s=>s.id===editingId);
    if(idx>-1){
      state[idx] = {...state[idx],name,party,age,bio};
    }
    editingId = null;
  }else{ // If creating a new candidate
    state.push({id:genId(),name,party,age,bio,votes:0});
  }
  save(); render(); onClear();
}

// Function to redraw the entire UI based on state
function render(){
  // ... filtering and search logic
  
  // Dynamic Card Rendering Loop
  filtered.forEach(c=>{
    // ... HTML template literal generation for the card
    listEl.appendChild(el);
  });
  
  // Update Stats and Chart
  totalCandidates.textContent = state.length;
  // ... renderChart();
}




Screenshots
 * UI Screenshot:


API / Database Integration
 * Integration: Utilizes the Web Storage API (localStorage) for data persistence. No external APIs or database calls are made.
9. Testing
Test Cases Table
| Test Case ID | Input | Expected Output | Actual Output | Status |
|---|---|---|---|---|
| TCC-01 | Add new candidate: Name "John Doe", Party "IND". | New card appears in list with 0 votes; Total Candidates +1. | Card rendered, stats updated. | PASS |
| TCU-01 | Click "Edit" on a candidate; change name, click Save. | Form clears; card updates with new name; editing mode disabled. | Name updated successfully. | PASS |
| TCV-01 | Click Vote button repeatedly (5 times). | Candidate's vote count increases by 5; Total Votes stat increases; Chart bar height changes. | All elements updated correctly. | PASS |
| TCPS-01 | Perform TCV-01; refresh browser. | Candidate retains the new vote count (votes +5). | Data loaded correctly from localStorage. | PASS |
| TCF-01 | Click "Party A" chip. | Only candidates with Party "Party A" are displayed. | Filtering logic successful. | PASS |
| TCS-01 | Enter "Vikram" into search bar. | Only the candidate "Vikram Rao" is displayed. | Search logic successful. | PASS |

Test Methods
 * Manual Testing (Systematic): All C.R.U.D. operations and UI elements were tested to ensure synchronization between the state array and the UI.
 * Persistence Check: Tested repeated save/reload cycles with browser hard refresh to confirm robust localStorage integration.
 * Responsiveness Check: Tested layout integrity across different screen widths using the browser's responsive design mode.

10. Results / Outputs
 * Final working screens: A professional, responsive portal displaying synchronized candidate data, stats, and a dynamic bar chart.
 * System workflow demonstration: Seamless user experience where data changes (voting, saving) result in immediate updates to the candidate list, the statistics counters, and the visual chart.
11. Challenges Faced
 * State Synchronization: The main challenge was ensuring that all relevant UI elements (listEl, totalCandidates, totalVotes, chart) were updated every time the state array was modified. Solution: centralizing all UI updates into a single, comprehensive render() function.
 * Data Typing: Ensuring that the age input was correctly parsed as an integer and that vote counts always remained numerical when loaded from the string-based localStorage.
 * Dynamic Event Binding: Attaching event listeners to dynamically generated elements (Vote/Edit buttons inside the render() loop) required careful attention to ensure listeners were applied correctly after each re-render.

12. Learning Outcomes
 * Technical skills learned: Gained expertise in designing single-file, state-driven frontend applications and the proper way to utilize localStorage for structured data. Improved skill in dynamic HTML generation using template literals.
 * Engineering approach gained: Learned the importance of a centralized rendering function to maintain UI synchronization across complex data models.
 * Problem-solving improvement: Developed greater proficiency in debugging multi-step processes (e.g., Edit flow: Button click -> Load data into form -> Update button text -> Save update to state).

13. Conclusion
The Election Candidate Portal successfully delivered a client-side C.R.U.D. application that is both functional and aesthetically modern. It serves as a comprehensive portfolio piece demonstrating mastery of Vanilla JavaScript for data management, responsive CSS layouts (Grid/Flexbox), and user interaction design.
 * Future scope / enhancements:
   * User Delete Functionality: Adding a "Delete Candidate" button.
   * Data Export/Import: Allowing users to download/upload the candidate list as a JSON file.
   * Advanced Charting: Replacing the simple CSS bar chart with a more sophisticated charting library (e.g., Chart.js) for detailed visualization.

14. Annexure
 * GitHub repository link: https://github.com/RiteshhVishwakarma/Election-Candidate-Portal---SIP
 * Student Coding Profile / Online Portfolio:
   
Source Code Submission

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Election Candidate Portal</title>
  <style>
    /* Reset & base */
    :root{
      --bg: #0f1724;
      --card: #0b1220;
      --accent: #F8B500; /* user earlier liked this color */
      --muted: #9aa6b2;
      --glass: rgba(255,255,255,0.04);
      --success: #16a34a;
      --danger: #ef4444;
      --radius: 12px;
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;background:linear-gradient(180deg,#071126 0%, #081424 60%);color:#e6eef6}
    a{color:inherit}

    .container{max-width:1100px;margin:28px auto;padding:20px}
    header{display:flex;gap:16px;align-items:center;justify-content:space-between}
    .brand{display:flex;gap:12px;align-items:center}
    .logo{width:52px;height:52px;border-radius:12px;background:linear-gradient(135deg,var(--accent),#00ADB5);display:flex;align-items:center;justify-content:center;font-weight:700;color:#072032}
    h1{font-size:20px;margin:0}
    p.lead{margin:0;color:var(--muted);font-size:13px}

    /* controls */
    .controls{display:flex;gap:12px;align-items:center}
    .search{background:var(--glass);padding:8px 10px;border-radius:999px;display:flex;align-items:center;gap:8px;width:320px}
    .search input{border:0;background:transparent;outline:none;color:inherit;width:100%}
    .btn{background:var(--accent);padding:10px 14px;border-radius:10px;border:0;cursor:pointer;font-weight:600}
    .btn.ghost{background:transparent;border:1px solid rgba(255,255,255,0.06)}

    main{display:grid;grid-template-columns:1fr 360px;gap:20px;margin-top:18px}

    /* list area */
    .panel{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:16px;border-radius:var(--radius);box-shadow:0 6px 30px rgba(2,6,23,0.6)}
    .filters{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px}
    .chip{padding:8px 10px;background:rgba(255,255,255,0.02);border-radius:999px;font-size:13px;cursor:pointer}
    .grid{display:grid;grid-template-columns:repeat(2,1fr);gap:12px}
    .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:12px;border-radius:12px;display:flex;gap:12px;align-items:center;border:1px solid rgba(255,255,255,0.03)}
    .avatar{width:64px;height:64px;border-radius:10px;flex-shrink:0;background:linear-gradient(180deg,#f5f5f5,#ddd);display:flex;align-items:center;justify-content:center;color:#072032;font-weight:700}
    .meta{flex:1}
    .meta h3{margin:0;font-size:16px}
    .meta p{margin:4px 0 0 0;color:var(--muted);font-size:13px}
    .actions{display:flex;gap:8px}
    .small{padding:6px 8px;border-radius:8px;border:0;cursor:pointer;font-size:13px}

    /* right sidebar */
    .sidebar .stats{display:flex;gap:12px;margin-bottom:12px}
    .stat{flex:1;background:rgba(255,255,255,0.02);padding:12px;border-radius:10px;text-align:center}
    .stat h4{margin:0;font-size:14px}
    .stat p{margin:6px 0 0 0;color:var(--muted);font-size:13px}
    .vote-chart{height:160px;background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.00));display:flex;align-items:flex-end;gap:8px;padding:12px;border-radius:10px}
    .bar{flex:1;border-radius:6px;display:flex;align-items:flex-end;justify-content:center;padding-bottom:6px;color:#072032;font-weight:700}

    /* form */
    .form-row{display:flex;gap:8px}
    .field{display:flex;flex-direction:column;margin-bottom:10px}
    .field label{font-size:13px;color:var(--muted);margin-bottom:6px}
    .field input, .field select, .field textarea{padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit;outline:none}

    footer{margin-top:22px;color:var(--muted);font-size:13px;text-align:center}

    /* responsive */
    @media (max-width:900px){
      main{grid-template-columns:1fr;}
      .grid{grid-template-columns:1fr}
      .search{width:220px}
      .container{padding:12px}
    }

    @media (max-width:420px){
      .search{display:none}
      .logo{width:44px;height:44px}
      h1{font-size:16px}
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div class="brand">
        <div class="logo">EC</div>
        <div>
          <h1>Election Candidate Portal</h1>
          <p class="lead">Browse candidates, add new ones and simulate votes — responsive for desktop & mobile</p>
        </div>
      </div>

      <div class="controls">
        <div class="search" title="Search candidates">
          <svg width="16" height="16" viewBox="0 0 24 24"><path fill="currentColor" d="M21 20l-4.35-4.35" stroke="currentColor" stroke-width="1.2" stroke-linecap="round" stroke-linejoin="round"></path><circle cx="11" cy="11" r="6" stroke="currentColor" stroke-width="1.2" fill="none"></circle></svg>
          <input id="q" placeholder="Search by name or party..." />
        </div>
        <button class="btn" id="addBtn">+ Add Candidate</button>
      </div>
    </header>

    <main>
      <section class="panel">
        <div class="filters">
          <div class="chip" data-party="all">All</div>
          <div class="chip" data-party="IND">Independent</div>
          <div class="chip" data-party="Party A">Party A</div>
          <div class="chip" data-party="Party B">Party B</div>
        </div>

        <div id="list" class="grid" aria-live="polite">
          <!-- candidate cards injected here -->
        </div>
        <footer>Tip: Click "Vote" to increase a candidate's vote count. Data persists locally in your browser.</footer>
      </section>

      <aside class="panel sidebar">
        <div class="stats">
          <div class="stat">
            <h4 id="totalCandidates">0</h4>
            <p>Total Candidates</p>
          </div>
          <div class="stat">
            <h4 id="totalVotes">0</h4>
            <p>Total Votes</p>
          </div>
        </div>

        <div class="vote-chart" id="chart" aria-hidden="false">
          <!-- bars injected -->
        </div>

        <hr style="border:none;height:1px;background:rgba(255,255,255,0.03);margin:12px 0;border-radius:2px" />

        <div>
          <h3 style="margin:0 0 8px 0">Add / Edit Candidate</h3>
          <form id="form">
            <div class="field">
              <label>Name</label>
              <input id="name" required />
            </div>
            <div class="field">
              <label>Party</label>
              <input id="party" placeholder="e.g. Party A or Independent" />
            </div>
            <div class="field">
              <label>Age</label>
              <input id="age" type="number" min="18" />
            </div>
            <div class="field">
              <label>Short Bio</label>
              <textarea id="bio" rows="3"></textarea>
            </div>
            <div style="display:flex;gap:8px">
              <button class="btn" type="submit">Save</button>
              <button type="button" class="btn ghost" id="clear">Clear</button>
            </div>
          </form>
        </div>
      </aside>
    </main>
  </div>

  <script>
    // Simple single-file app: stores data in localStorage. Suitable for prototyping only.
    const STORAGE_KEY = 'election_portal_v1';
    const sample = [
      {id:genId(),name:'Asha Patel',party:'Party A',age:42,bio:'Community leader focused on education.',votes:124},
      {id:genId(),name:'Vikram Rao',party:'Party B',age:50,bio:'Business background, pro-infrastructure.',votes:98},
      {id:genId(),name:'Meera Khan',party:'IND',age:36,bio:'Youth activist & volunteer.',votes:212}
    ];

    let state = load() || sample;
    let editingId = null;

    // elements
    const listEl = document.getElementById('list');
    const totalCandidates = document.getElementById('totalCandidates');
    const totalVotes = document.getElementById('totalVotes');
    const chart = document.getElementById('chart');
    const q = document.getElementById('q');

    // form
    const form = document.getElementById('form');
    const nameIn = document.getElementById('name');
    const partyIn = document.getElementById('party');
    const ageIn = document.getElementById('age');
    const bioIn = document.getElementById('bio');
    const addBtn = document.getElementById('addBtn');
    const clearBtn = document.getElementById('clear');

    // init
    render();
    attachListeners();

    function attachListeners(){
      form.addEventListener('submit', onSave);
      clearBtn.addEventListener('click', onClear);
      addBtn.addEventListener('click', ()=>{scrollToForm();});
      q.addEventListener('input', render);

      document.querySelectorAll('.chip').forEach(c=>c.addEventListener('click', (e)=>{filterByParty(e.target.dataset.party);}))
    }

    function onSave(e){
      e.preventDefault();
      const name = nameIn.value.trim();
      if(!name){alert('Name required');return}
      const party = partyIn.value.trim() || 'Independent';
      const age = parseInt(ageIn.value) || null;
      const bio = bioIn.value.trim();

      if(editingId){
        const idx = state.findIndex(s=>s.id===editingId);
        if(idx>-1){
          state[idx] = {...state[idx],name,party,age,bio};
        }
        editingId = null;
      }else{
        state.push({id:genId(),name,party,age,bio,votes:0});
      }
      save(); render(); onClear();
    }

    function onClear(){
      nameIn.value=''; partyIn.value=''; ageIn.value=''; bioIn.value=''; editingId=null; form.querySelector('button[type=submit]').textContent='Save'
    }

    function scrollToForm(){
      // jump to sidebar form on mobile/desktop
      document.querySelector('.sidebar').scrollIntoView({behavior:'smooth'});
    }

    function render(){
      const filter = (document.querySelector('.chip.active')||{}).dataset?.party || 'all';
      const query = q.value.trim().toLowerCase();
      const filtered = state.filter(c=>{
        if(filter && filter!=='all' && c.party !== filter) return false;
        if(query){
          return c.name.toLowerCase().includes(query) || (c.party||'').toLowerCase().includes(query) || (c.bio||'').toLowerCase().includes(query)
        }
        return true;
      });

      listEl.innerHTML='';
      if(filtered.length===0){
        listEl.innerHTML='<div style="color:var(--muted);padding:18px">No candidates found. Add one using the form.</div>';
      }

      filtered.forEach(c=>{
        const el = document.createElement('div'); el.className='card';
        el.innerHTML = `
          <div class="avatar">${initials(c.name)}</div>
          <div class="meta">
            <h3>${escapeHtml(c.name)} <span style="font-size:12px;color:var(--muted);font-weight:600">• ${escapeHtml(c.party||'Independent')}</span></h3>
            <p>${escapeHtml(c.bio||'—')}</p>
          </div>
          <div style="display:flex;flex-direction:column;align-items:flex-end;gap:8px">
            <div style="font-weight:700">${c.votes}</div>
            <div class="actions">
              <button class="small ghost" data-id="${c.id}" data-action="edit">Edit</button>
              <button class="small" data-id="${c.id}" data-action="vote">Vote</button>
            </div>
          </div>
        `;
        listEl.appendChild(el);
      });

      // attach card listeners
      listEl.querySelectorAll('button').forEach(b=>b.addEventListener('click', onCardAction));

      // stats
      totalCandidates.textContent = state.length;
      totalVotes.textContent = state.reduce((s,i)=>s+i.votes,0);

      renderChart();
    }

    function onCardAction(e){
      const id = e.currentTarget.dataset.id;
      const act = e.currentTarget.dataset.action;
      if(act==='vote'){
        voteFor(id);
      }else if(act==='edit'){
        editCandidate(id);
      }
    }

    function voteFor(id){
      const idx = state.findIndex(s=>s.id===id); if(idx===-1) return;
      state[idx].votes += 1; save(); render();
    }

    function editCandidate(id){
      const c = state.find(s=>s.id===id); if(!c) return;
      nameIn.value=c.name; partyIn.value=c.party; ageIn.value=c.age||''; bioIn.value=c.bio||''; editingId=c.id; form.querySelector('button[type=submit]').textContent='Update'; scrollToForm();
    }

    function renderChart(){
      chart.innerHTML='';
      if(state.length===0) return;
      const max = Math.max(...state.map(s=>s.votes),1);
      state.slice(0,6).forEach(s=>{
        const h = Math.round((s.votes/max)*100);
        const div = document.createElement('div'); div.className='bar'; div.style.height = (10 + h) + '%'; div.style.background = `linear-gradient(180deg, var(--accent), #00ADB5)`; div.textContent = s.votes;
        div.title = `${s.name} — ${s.votes} votes`;
        chart.appendChild(div);
      });
    }

    function filterByParty(party){
      document.querySelectorAll('.chip').forEach(c=>c.classList.remove('active'));
      const el = Array.from(document.querySelectorAll('.chip')).find(x=>x.dataset.party===party);
      if(el) el.classList.add('active');
      render();
    }

    function save(){
      localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
    }
    function load(){
      try{const s = localStorage.getItem(STORAGE_KEY); return s?JSON.parse(s):null}catch(e){return null}
    }

    // util
    function genId(){return 'c_'+Math.random().toString(36).slice(2,9)}
    function initials(name){return name.split(' ').map(n=>n[0]).slice(0,2).join('').toUpperCase()}
    function escapeHtml(s){return (s+'').replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;')}

    // small UX: clear filter when clicking outside chips
    document.addEventListener('click', (e)=>{
      if(e.target.closest('.chip')) return;
      // do nothing for now
    });

    // keyboard shortcut: / to focus search
    document.addEventListener('keydown', e=>{ if(e.key==='/' && document.activeElement.tagName!=='INPUT' && document.activeElement.tagName!=='TEXTAREA'){ e.preventDefault(); q.focus(); } });

  </script>
</body>
</html>

Complete HTML, CSS, and JavaScript Code

