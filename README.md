# Gen-Ai-Hackathon
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>MoodMate – Youth Mental Wellness</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Icons -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    :root { color-scheme: light dark; }
    html, body { font-family: 'Inter', system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, 'Apple Color Emoji', 'Segoe UI Emoji'; }
    .glass { backdrop-filter: blur(10px); background: rgba(255,255,255,0.7); }
    .dark .glass { background: rgba(0,0,0,0.3); }
    .card { @apply rounded-2xl p-5 shadow-lg; }
    .btn { @apply rounded-2xl px-4 py-2 font-medium shadow; }
    .btn-primary { @apply bg-indigo-600 text-white hover:bg-indigo-700; }
    .btn-soft { @apply bg-indigo-50 text-indigo-700 hover:bg-indigo-100; }
    .tag { @apply inline-flex items-center gap-1 rounded-full px-3 py-1 text-sm bg-slate-100 dark:bg-slate-800; }
  </style>
</head>
<body class="min-h-screen bg-gradient-to-b from-indigo-50 to-white dark:from-slate-900 dark:to-slate-950 text-slate-900 dark:text-slate-100">
  <header class="sticky top-0 z-40 backdrop-blur bg-white/60 dark:bg-slate-900/60 border-b border-slate-200/40 dark:border-slate-700/50">
    <div class="max-w-6xl mx-auto px-4 py-4 flex items-center justify-between">
      <div class="flex items-center gap-3">
        <div class="h-10 w-10 rounded-2xl bg-indigo-600 grid place-items-center text-white font-black">MM</div>
        <div>
          <h1 class="text-xl font-extrabold tracking-tight">MoodMate</h1>
          <p class="text-xs text-slate-500">Private mood journal • AI coping ideas • Student-first</p>
        </div>
      </div>
      <div class="flex gap-2">
        <button id="toggleTheme" class="btn btn-soft">Toggle Theme</button>
        <button id="exportJson" class="btn btn-soft">Export Data</button>
        <label class="btn btn-soft cursor-pointer">
          <input id="importJson" type="file" accept="application/json" class="hidden" />
          Import Data
        </label>
      </div>
    </div>
  </header>

  <main class="max-w-6xl mx-auto px-4 py-8 grid gap-6 lg:grid-cols-3">
    <!-- Left Column: Check-in -->
    <section class="lg:col-span-2 glass card">
      <h2 class="text-2xl font-bold mb-1">Daily Check‑in</h2>
      <p class="text-sm text-slate-500 mb-4">Log your feelings in under a minute. Your data stays on your device (localStorage). You can export anytime.</p>

      <form id="checkinForm" class="grid gap-4">
        <div class="grid md:grid-cols-2 gap-4">
          <div>
            <label class="text-sm font-semibold">Mood (1 = low, 10 = great)</label>
            <input id="mood" type="range" min="1" max="10" value="5" class="w-full"/>
            <div class="text-sm text-slate-500">Current: <span id="moodVal" class="font-semibold">5</span></div>
          </div>
          <div>
            <label class="text-sm font-semibold">Energy</label>
            <input id="energy" type="range" min="1" max="10" value="5" class="w-full"/>
            <div class="text-sm text-slate-500">Current: <span id="energyVal" class="font-semibold">5</span></div>
          </div>
        </div>
        <div>
          <label class="text-sm font-semibold">Emotions (choose any)</label>
          <div class="flex flex-wrap gap-2 mt-2" id="emotionTags"></div>
        </div>
        <div>
          <label class="text-sm font-semibold">What’s going on? (optional)</label>
          <textarea id="notes" rows="3" class="w-full p-3 rounded-xl border border-slate-200 dark:border-slate-700 bg-white/70 dark:bg-slate-900/40" placeholder="e.g., exams coming up, argument with friend, feeling overwhelmed"></textarea>
        </div>
        <div class="flex items-center justify-between">
          <div class="text-xs text-slate-500">Tip: Add at least one emotion tag for better suggestions.</div>
          <button class="btn btn-primary" type="submit">Save Check‑in</button>
        </div>
      </form>
    </section>

    <!-- Right Column: AI Coach -->
    <aside class="glass card">
      <h2 class="text-2xl font-bold mb-1">AI Coping Coach</h2>
      <p class="text-sm text-slate-500 mb-4">Private, supportive ideas based on your check‑in. Not a medical service.</p>

      <div class="grid gap-3 mb-4">
        <button id="suggestBtn" class="btn btn-primary w-full">Get Support Ideas</button>
        <button id="breathBtn" class="btn btn-soft w-full">Start 60s Breathing</button>
      </div>

      <div id="suggestions" class="space-y-3"></div>

      <div class="mt-6 text-xs text-slate-500 border-t border-slate-200 dark:border-slate-700 pt-3">
        <p>If you’re in crisis or thinking about self‑harm, please reach out to local emergency services or a trusted adult immediately.</p>
        <ul class="list-disc ml-5 mt-2">
          <li>India: <a class="underline" href="tel:9152987821">9152987821 (Kiran Helpline)</a></li>
          <li>Worldwide: <a class="underline" href="https://www.opencounseling.com/suicide-hotlines" target="_blank">International helplines</a></li>
        </ul>
      </div>

      <div class="mt-4">
        <label class="text-xs font-semibold">(Optional) OpenAI API Key</label>
        <input id="apiKey" type="password" placeholder="sk-..." class="w-full mt-1 p-2 rounded-xl border border-slate-200 dark:border-slate-700 bg-white/70 dark:bg-slate-900/40" />
        <p class="text-xs text-slate-500 mt-1">If provided, suggestions will be enhanced using your key locally from the browser. Your key is stored only in this device’s localStorage.</p>
      </div>
    </aside>

    <!-- Insights -->
    <section class="lg:col-span-2 glass card">
      <div class="flex items-center justify-between">
        <h2 class="text-2xl font-bold">Insights</h2>
        <div class="flex gap-2">
          <button id="clearData" class="btn btn-soft">Clear All Data</button>
          <button id="printBtn" class="btn btn-soft">Print / Save PDF</button>
        </div>
      </div>

      <div class="grid md:grid-cols-2 gap-6 mt-4">
        <div>
          <h3 class="font-semibold mb-2">Mood Trend</h3>
          <canvas id="moodChart" height="160"></canvas>
        </div>
        <div>
          <h3 class="font-semibold mb-2">Your Journals</h3>
          <div id="journalList" class="space-y-3 max-h-80 overflow-auto pr-2"></div>
        </div>
      </div>
    </section>

    <!-- Learn -->
    <section class="glass card">
      <h2 class="text-2xl font-bold">Learn: Quick Skills</h2>
      <div class="space-y-3 text-sm text-slate-600 dark:text-slate-300 mt-2">
        <details class="rounded-xl border border-slate-200 dark:border-slate-700 p-4">
          <summary class="font-semibold cursor-pointer">5‑4‑3‑2‑1 Grounding</summary>
          <p class="mt-2">Name 5 things you see, 4 you can touch, 3 you can hear, 2 you can smell, 1 you can taste. This can reduce anxiety by anchoring you in the present.</p>
        </details>
        <details class="rounded-xl border border-slate-200 dark:border-slate-700 p-4">
          <summary class="font-semibold cursor-pointer">Cognitive Reframing</summary>
          <p class="mt-2">Notice a negative thought ("I’ll fail exams"). Challenge it: What evidence supports/contradicts it? Create a balanced thought ("I’m behind, but I have a plan and time").</p>
        </details>
        <details class="rounded-xl border border-slate-200 dark:border-slate-700 p-4">
          <summary class="font-semibold cursor-pointer">Sleep Reset</summary>
          <p class="mt-2">Aim for consistent sleep/wake times, reduce screens 60 minutes before bed, and consider a 10‑minute wind‑down (stretching, journaling, breathing).</p>
        </details>
      </div>
    </section>
  </main>

  <footer class="max-w-6xl mx-auto px-4 py-6 text-xs text-slate-500">
    <p>Disclaimer: MoodMate provides educational support only and is not a substitute for professional care. In emergencies, contact local services.</p>
  </footer>

  <script>
    // ===== Utilities & State =====
    const $ = (sel) => document.querySelector(sel);
    const $$ = (sel) => Array.from(document.querySelectorAll(sel));

    const store = {
      get() { return JSON.parse(localStorage.getItem('moodmate:data') || '[]'); },
      set(data) { localStorage.setItem('moodmate:data', JSON.stringify(data)); refresh(); },
      clear() { localStorage.removeItem('moodmate:data'); refresh(); },
    };

    const settings = {
      get apiKey() { return localStorage.getItem('moodmate:openai') || ''; },
      set apiKey(v) { localStorage.setItem('moodmate:openai', v); },
      get theme() { return localStorage.getItem('theme') || 'auto'; },
      set theme(v) { localStorage.setItem('theme', v); applyTheme(); },
    };

    function applyTheme(){
      const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
      const t = settings.theme;
      document.documentElement.classList.toggle('dark', t === 'dark' || (t === 'auto' && prefersDark));
    }

    // ===== UI Init =====
    const emotions = ['happy','calm','anxious','sad','angry','lonely','stressed','grateful','tired','excited','overwhelmed','confident'];
    const selected = new Set();
    const tagsWrap = $('#emotionTags');
    emotions.forEach(e => {
      const b = document.createElement('button');
      b.type='button';
      b.className='tag';
      b.innerHTML = `<span>#${e}</span>`;
      b.addEventListener('click', ()=>{ b.classList.toggle('ring-2'); if(selected.has(e)) selected.delete(e); else selected.add(e);});
      tagsWrap.appendChild(b);
    });

    // Range value mirrors
    const mood = $('#mood'), energy = $('#energy');
    const mv = $('#moodVal'), ev = $('#energyVal');
    mood.oninput = ()=> mv.textContent = mood.value;
    energy.oninput = ()=> ev.textContent = energy.value;

    // ===== Check-in Save =====
    $('#checkinForm').addEventListener('submit', (e)=>{
      e.preventDefault();
      const entry = {
        id: crypto.randomUUID(),
        ts: new Date().toISOString(),
        mood: Number(mood.value),
        energy: Number(energy.value),
        emotions: Array.from(selected),
        notes: $('#notes').value.trim(),
      };
      const data = store.get();
      data.push(entry);
      store.set(data);
      // Reset minimal
      $('#notes').value=''; selected.clear(); $$('#emotionTags .tag').forEach(t=>t.classList.remove('ring-2'));
    });

    // ===== AI Suggestions =====
    const patterns = [
      { when: e=> e.mood <= 3 || e.emotions.includes('overwhelmed'), tips:[
        'Try the 5‑4‑3‑2‑1 grounding exercise to anchor in the present.',
        'Break tasks into 10‑minute chunks. Do just the first chunk.',
        'Message one supportive friend. Ask for a quick check‑in call.',
      ]},
      { when: e=> e.emotions.includes('anxious') || /exam|deadline|result/i.test(e.notes), tips:[
        'Write worries, then list evidence for/against each. Form a balanced thought.',
        'Box breathing: inhale 4, hold 4, exhale 4, hold 4. Repeat for 60 seconds.',
        'Pick one small, controllable action (e.g., outline notes for 15 minutes).',
      ]},
      { when: e=> e.emotions.includes('sad') || e.emotions.includes('lonely'), tips:[
        'Do a 10‑minute walk outside and notice 5 pleasant things.',
        'Text a friend or join a campus club chat; ask one open question.',
        'Write three micro‑gratitudes from today.',
      ]},
      { when: e=> e.emotions.includes('angry'), tips:[
        'Physically discharge: 20 squats or 2‑minute brisk walk, then name the need behind the anger.',
        'Use “I‑statements”: I feel … when … because … I need …',
        'Delay big decisions for 20 minutes; revisit with calmer mind.',
      ]},
    ];

    async function maybeLLMSupplement(prompt){
      const key = $('#apiKey').value || settings.apiKey;
      if(!key) return null;
      try {
        const res = await fetch('https://api.openai.com/v1/chat/completions',{
          method:'POST',
          headers:{'Content-Type':'application/json','Authorization':`Bearer ${key}`},
          body: JSON.stringify({
            model: 'gpt-4o-mini',
            messages:[{role:'system',content:'You are a brief, supportive youth mental wellness coach. Give 4 numbered, specific, safe, non-clinical coping ideas in 80 words total. Avoid medical claims.'},{role:'user',content: prompt}],
            temperature:0.6
          })
        });
        const data = await res.json();
        const text = data?.choices?.[0]?.message?.content?.trim();
        return text || null;
      } catch(err){ return null; }
    }

    $('#suggestBtn').addEventListener('click', async ()=>{
      const data = store.get();
      const entry = data[data.length-1];
      const out = $('#suggestions'); out.innerHTML='';
      if(!entry){ out.innerHTML = '<p class="text-sm text-slate-500">Log a check‑in first to get personalized ideas.</p>'; return; }

      // Local tips
      let tips = [];
      for(const p of patterns){ if(p.when(entry)) tips = tips.concat(p.tips); }
      if(tips.length === 0){ tips = ['Do a gentle 5‑minute stretch routine.', 'Write one worry and one possible next step.', 'Drink water and step outside for fresh air.', 'Queue a feel‑good playlist for 10 minutes.']; }

      const card = document.createElement('div');
      card.className='rounded-2xl border border-slate-200 dark:border-slate-700 p-4';
      card.innerHTML = '<h4 class="font-semibold mb-2">Personalized Tips</h4>' + tips.map(t=>`<div class="flex gap-2"><span>•</span><p>${t}</p></div>`).join('');
      out.appendChild(card);

      // Optional LLM
      const key = $('#apiKey').value || settings.apiKey;
      if(key){ settings.apiKey = key; }
      if(key){
        const prompt = `Mood: ${entry.mood}/10. Energy: ${entry.energy}/10. Emotions: ${entry.emotions.join(', ')||'none'}. Notes: ${entry.notes||'–'}. Provide brief coping ideas.`;
        const llm = await maybeLLMSupplement(prompt);
        if(llm){
          const llmCard = document.createElement('div');
          llmCard.className='rounded-2xl border border-indigo-200 dark:border-indigo-700/60 p-4 bg-indigo-50/50 dark:bg-indigo-900/20';
          llmCard.innerHTML = '<h4 class="font-semibold mb-2">AI‑Enhanced Ideas</h4>' + llm.split(/\n+/).map(l=>`<p>${l}</p>`).join('');
          out.appendChild(llmCard);
        }
      }
    });

    // Breathing timer
    let breatheTimer = null;
    $('#breathBtn').addEventListener('click', ()=>{
      const out = $('#suggestions');
      const box = document.createElement('div');
      box.className='rounded-2xl border border-emerald-200 dark:border-emerald-700 p-4 bg-emerald-50/50 dark:bg-emerald-900/20';
      const phase = document.createElement('div');
      const time = document.createElement('div');
      phase.className='text-lg font-semibold';
      time.className='text-5xl font-black mt-1';
      box.appendChild(phase); box.appendChild(time); out.prepend(box);

      const seq = [
        {name:'Inhale', secs:4},
        {name:'Hold', secs:4},
        {name:'Exhale', secs:4},
        {name:'Hold', secs:4},
      ];
      let total = 60, idx = 0, left = seq[0].secs;
      phase.textContent = seq[0].name; time.textContent = left;
      clearInterval(breatheTimer);
      breatheTimer = setInterval(()=>{
        left--; total--; time.textContent = left;
        if(left<=0){ idx=(idx+1)%seq.length; left = seq[idx].secs; phase.textContent = seq[idx].name; }
        if(total<=0){ clearInterval(breatheTimer); phase.textContent='Done 🎉'; }
      }, 1000);
    });

    // Journals list + chart
    let chart;
    function refresh(){
      // Journals
      const list = $('#journalList'); list.innerHTML='';
      const data = store.get().slice().reverse();
      for(const e of data){
        const wrap = document.createElement('div');
        wrap.className='p-3 rounded-xl border border-slate-200 dark:border-slate-700';
        const dt = new Date(e.ts);
        wrap.innerHTML = `
          <div class="flex items-center justify-between">
            <div class="font-semibold">${dt.toLocaleString()}</div>
            <div class="text-xs">Mood <span class="font-semibold">${e.mood}</span> • Energy <span class="font-semibold">${e.energy}</span></div>
          </div>
          <div class="mt-2 flex flex-wrap gap-2">${(e.emotions||[]).map(x=>`<span class="tag">#${x}</span>`).join('')}</div>
          ${e.notes? `<p class="mt-2 text-sm">${e.notes}</p>`:''}
        `;
        list.appendChild(wrap);
      }

      // Chart
      const ctx = $('#moodChart').getContext('2d');
      const d = store.get();
      const labels = d.map(x=> new Date(x.ts).toLocaleDateString());
      const moods = d.map(x=> x.mood);
      if(chart) chart.destroy();
      chart = new Chart(ctx, {
        type:'line',
        data:{ labels, datasets:[{ label:'Mood', data:moods, tension:0.3, fill:false }]},
        options:{
          responsive:true,
          scales:{ y:{ suggestedMin:0, suggestedMax:10 } },
          plugins:{ legend:{ display:true } }
        }
      });
    }

    // Export / Import / Clear / Print
    $('#exportJson').addEventListener('click', ()=>{
      const blob = new Blob([JSON.stringify(store.get(), null, 2)], {type:'application/json'});
      const url = URL.createObjectURL(blob);
      const a = Object.assign(document.createElement('a'), { href: url, download: 'moodmate-data.json' });
      document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    });
    $('#importJson').addEventListener('change', async (e)=>{
      const file = e.target.files[0]; if(!file) return;
      const text = await file.text();
      try { const json = JSON.parse(text); if(Array.isArray(json)) store.set(json); } catch(err){ alert('Invalid file'); }
      e.target.value = '';
    });
    $('#clearData').addEventListener('click', ()=>{ if(confirm('Clear all mood entries?')) store.clear(); });
    $('#printBtn').addEventListener('click', ()=> window.print());

    // Theme
    $('#toggleTheme').addEventListener('click', ()=>{
      const curr = settings.theme;
      settings.theme = curr === 'dark' ? 'light' : curr === 'light' ? 'auto' : 'dark';
    });

    // Init
    applyTheme();
    $('#apiKey').value = settings.apiKey;
    refresh();
  </script>

  <!--
  =============================================
  MoodMate – Hackathon Hand‑off Notes
  =============================================
  Problem: Student mental wellness needs accessible, private, engaging tools.

  This single‑file website provides:
  • Private daily check‑ins (mood, energy, tags, notes) – stored locally
  • AI Coping Coach – rule‑based tips + optional OpenAI enhancement (bring your own key)
  • 60‑second box‑breathing guide
  • Charts & journals – track trends
  • Data portability – export/import JSON; print to PDF for reports
  • Crisis resources block

  How to run: Open this file (index.html) in any modern browser. No server required.
  Optional AI: Enter an OpenAI API key. Requests are made from the browser and the key is stored locally.
  Security/Privacy: Data remains in localStorage; provide a privacy note in your demo.

  Extension ideas (post‑hack):
  • Accounts with end‑to‑end encrypted sync
  • School/college admin dashboard with aggregated, de‑identified metrics
  • Streaks, coaching quests, push notifications (as PWA)
  • Multilingual UI (add i18n strings)

  -->
</body>
</html>
