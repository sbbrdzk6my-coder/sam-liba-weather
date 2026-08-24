from pathlib import Path

import zipfile, textwrap

root = Path("/mnt/data/mochi-weather")

root.mkdir(parents=True, exist_ok=True)

files = {

"index.html": r'''<!DOCTYPE html>

<html lang="id">

<head>

  <meta charset="UTF-8">

  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <meta name="theme-color" content="#9edcff">

  <meta name="description" content="Mochi Weather — cute, simple weather app.">

  <link rel="manifest" href="manifest.json">

  <link rel="icon" href="icon.svg" type="image/svg+xml">

  <title>Mochi Weather ☁️</title>

  <link rel="preconnect" href="https://fonts.googleapis.com">

  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

  <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;500;600;700;800;900&display=swap" rel="stylesheet">

  <link rel="stylesheet" href="style.css">

</head>

<body>

  <div class="sky-decor" aria-hidden="true">

    <span class="cloud cloud-1">☁️</span>

    <span class="cloud cloud-2">☁️</span>

    <span class="spark spark-1">✦</span>

    <span class="spark spark-2">✧</span>

  </div>

  <main class="app-shell">

    <header class="topbar">

      <a class="brand" href="#" aria-label="Mochi Weather home">

        <span class="brand-mascot">☁️</span>

        <span>Mochi Weather</span>

      </a>

      <div class="top-actions">

        <button id="locationBtn" class="icon-btn" title="Gunakan lokasi saya" aria-label="Gunakan lokasi saya">⌖</button>

        <button id="themeBtn" class="icon-btn" title="Ganti tema" aria-label="Ganti tema">☾</button>

      </div>

    </header>

    <section class="search-area">

      <div class="search-box">

        <span class="search-icon">⌕</span>

        <input id="cityInput" type="search" placeholder="Cari kota..." autocomplete="off" aria-label="Cari kota">

        <button id="searchBtn" class="search-btn">Cari</button>

      </div>

      <div id="searchResults" class="search-results" hidden></div>

      <p class="search-hint">Coba cari: <button class="city-chip" data-city="Pamekasan">Pamekasan</button> <button class="city-chip" data-city="Surabaya">Surabaya</button> <button class="city-chip" data-city="Jakarta">Jakarta</button></p>

    </section>

    <div id="status" class="status" aria-live="polite"></div>

    <section id="weatherView" hidden>

      <div class="hero-card">

        <div class="hero-copy">

          <div class="location-line">

            <span>📍</span>

            <div>

              <h1 id="cityName">Pamekasan</h1>

              <p id="countryName">Indonesia</p>

            </div>

          </div>

          <p id="dateText" class="date-text"></p>

          <div class="temperature-row">

            <span id="temperature">--</span><sup>°</sup>

          </div>

          <h2 id="condition">Memuat...</h2>

          <p id="feelsLike">Terasa seperti --°</p>

        </div>

        <div class="mochi-scene" id="mochiScene" aria-label="Maskot cuaca">

          <div class="weather-orb" id="weatherOrb">☀️</div>

          <div class="mochi" id="mochi">◕ᴗ◕</div>

          <div class="mochi-caption" id="mochiCaption">Mochi lagi ceria!</div>

        </div>

      </div>

      <section class="section">

        <div class="section-title">

          <h2>Today</h2>

          <button id="refreshBtn" class="small-btn">↻ Refresh</button>

        </div>

        <div class="details-grid">

          <article class="detail-card"><span>💧</span><div><small>Humidity</small><strong id="humidity">--%</strong></div></article>

          <article class="detail-card"><span>💨</span><div><small>Wind</small><strong id="wind">-- km/h</strong></div></article>

          <article class="detail-card"><span>🌡️</span><div><small>Feels like</small><strong id="feelsDetail">--°C</strong></div></article>

          <article class="detail-card"><span>🌧️</span><div><small>Rain chance</small><strong id="rainChance">--%</strong></div></article>

        </div>

      </section>

      <section class="section">

        <div class="section-title"><h2>Hourly</h2><span class="section-note">Next 12 hours</span></div>

        <div id="hourly" class="hourly-list"></div>

      </section>

      <section class="section">

        <div class="section-title"><h2>7-Day Forecast</h2><span class="section-note">Plan your week ✨</span></div>

        <div id="daily" class="daily-list"></div>

      </section>

    </section>

    <section id="welcome" class="welcome-card">

      <div class="welcome-illustration">☁️<span>◕ᴗ◕</span>☀️</div>

      <h1>Where should Mochi<br>check the weather?</h1>

      <p>Masukkan nama kota dan biarkan Mochi membawakan ramalan cuacanya. ✨</p>

      <button class="primary-btn" data-city="Pamekasan">Coba Pamekasan</button>

    </section>

    <div id="errorCard" class="error-card" hidden>

      <div class="error-face">⊙﹏⊙</div>

      <h2>Oops! Mochi kehilangan sinyal ☁️</h2>

      <p id="errorText">Coba cek koneksi internetmu lalu ulangi.</p>

      <button id="retryBtn" class="primary-btn">Try Again</button>

    </div>

    <footer>Made with ☁️ + JavaScript · Weather data by Open-Meteo</footer>

  </main>

  <script src="script.js"></script>

</body>

</html>''',

"style.css": r''':root{

  --bg:#edf8ff; --bg2:#dff1ff; --card:rgba(255,255,255,.72); --text:#19324a;

  --muted:#6d8192; --primary:#71bff0; --accent:#ffd978; --border:rgba(255,255,255,.9);

  --shadow:0 20px 60px rgba(65,125,165,.14); --radius:28px;

}

*{box-sizing:border-box}

html{scroll-behavior:smooth}

body{

  margin:0; min-height:100vh; font-family:"Nunito",system-ui,sans-serif; color:var(--text);

  background:radial-gradient(circle at 15% 10%,#fff 0 12%,transparent 30%),linear-gradient(145deg,var(--bg),var(--bg2));

  overflow-x:hidden; transition:.35s ease;

}

body.dark{

  --bg:#101b2b; --bg2:#182b43; --card:rgba(28,45,66,.78); --text:#edf7ff; --muted:#a7bbcc;

  --border:rgba(255,255,255,.08); --shadow:0 20px 60px rgba(0,0,0,.25);

  background:radial-gradient(circle at 80% 0%,#294763 0 8%,transparent 30%),linear-gradient(145deg,var(--bg),var(--bg2));

}

button,input{font:inherit}

button{cursor:pointer}

.sky-decor{position:fixed;inset:0;pointer-events:none;overflow:hidden;z-index:-1}

.cloud,.spark{position:absolute;opacity:.5;filter:blur(.1px);animation:float 10s ease-in-out infinite}

.cloud{font-size:4rem}.cloud-1{top:10%;left:-2%;animation-delay:-3s}.cloud-2{top:23%;right:-2%;font-size:3rem;animation-delay:-7s}

.spark{font-size:1.5rem}.spark-1{top:18%;left:15%}.spark-2{top:8%;right:17%;animation-delay:-5s}

@keyframes float{50%{transform:translate(20px,-10px)}}

.app-shell{width:min(1120px,calc(100% - 32px));margin:auto;padding:24px 0 28px}

.topbar{display:flex;align-items:center;justify-content:space-between;gap:20px}

.brand{display:flex;align-items:center;gap:10px;text-decoration:none;color:var(--text);font-size:1.2rem;font-weight:900}

.brand-mascot{display:grid;place-items:center;width:42px;height:42px;border-radius:15px;background:var(--card);box-shadow:var(--shadow);font-size:1.45rem}

.top-actions{display:flex;gap:9px}.icon-btn,.small-btn{

  border:1px solid var(--border);background:var(--card);color:var(--text);box-shadow:var(--shadow);backdrop-filter:blur(18px)

}

.icon-btn{width:42px;height:42px;border-radius:14px;font-size:1.15rem}

.search-area{margin:38px auto 18px;max-width:720px;position:relative}

.search-box{display:flex;align-items:center;gap:8px;background:var(--card);border:1px solid var(--border);box-shadow:var(--shadow);backdrop-filter:blur(20px);padding:8px 8px 8px 16px;border-radius:20px}

.search-icon{font-size:1.35rem}.search-box input{min-width:0;flex:1;border:0;outline:0;background:transparent;color:var(--text);font-weight:700;font-size:1rem}

.search-btn,.primary-btn{border:0;background:var(--text);color:var(--bg);font-weight:900}

.search-btn{padding:11px 18px;border-radius:14px}

.search-hint{color:var(--muted);font-size:.86rem;text-align:center;margin:10px 0}.city-chip{border:0;background:transparent;color:#4d9bc9;font-weight:800;padding:0 3px}

.search-results{position:absolute;top:65px;left:0;right:0;background:var(--card);border:1px solid var(--border);box-shadow:var(--shadow);border-radius:18px;overflow:hidden;z-index:5;backdrop-filter:blur(20px)}

.result-btn{display:block;width:100%;padding:13px 16px;text-align:left;border:0;border-bottom:1px solid var(--border);background:transparent;color:var(--text)}

.result-btn:hover{background:rgba(113,191,240,.12)}

.status{text-align:center;min-height:24px;color:var(--muted);font-weight:700}

.hero-card{

  display:flex;justify-content:space-between;align-items:center;gap:30px;min-height:350px;padding:38px 44px;

  border:1px solid var(--border);background:linear-gradient(135deg,rgba(255,255,255,.78),rgba(255,255,255,.43));

  box-shadow:var(--shadow);backdrop-filter:blur(22px);border-radius:36px;overflow:hidden;position:relative

}

.dark .hero-card{background:linear-gradient(135deg,rgba(37,59,84,.85),rgba(27,45,66,.62))}

.location-line{display:flex;align-items:center;gap:11px}.location-line>span{font-size:1.25rem}

h1,h2,p{margin-top:0}.location-line h1{font-size:1.55rem;margin:0;font-weight:900}.location-line p{margin:1px 0;color:var(--muted);font-weight:700}

.date-text{margin:20px 0 8px;color:var(--muted);font-weight:700}.temperature-row{display:flex;align-items:flex-start;line-height:.9}

#temperature{font-size:clamp(5rem,11vw,8rem);letter-spacing:-7px;font-weight:900}.temperature-row sup{font-size:2.2rem;font-weight:900;margin-top:10px}

.hero-copy h2{margin:12px 0 4px;font-size:1.45rem}.hero-copy #feelsLike{color:var(--muted);font-weight:700}

.mochi-scene{width:320px;height:260px;display:grid;place-items:center;position:relative;flex-shrink:0}

.weather-orb{font-size:5rem;position:absolute;top:10px;right:25px;animation:orb 4s ease-in-out infinite}

@keyframes orb{50%{transform:translateY(-8px) rotate(5deg)}}

.mochi{width:180px;height:150px;border-radius:48% 48% 45% 45%;display:grid;place-items:center;background:#fff7e8;box-shadow:inset -10px -8px 0 rgba(232,202,157,.25),0 22px 30px rgba(62,101,128,.16);font-size:3.1rem;font-weight:900;letter-spacing:3px;animation:bounce 3.5s ease-in-out infinite}

@keyframes bounce{50%{transform:translateY(-8px)}}

.mochi-caption{position:absolute;bottom:8px;background:var(--card);padding:8px 14px;border-radius:999px;font-weight:800;font-size:.84rem;box-shadow:var(--shadow)}

.section{margin-top:30px}.section-title{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px}.section-title h2{font-size:1.25rem;margin:0}.section-note{color:var(--muted);font-size:.85rem;font-weight:800}

.small-btn{border-radius:12px;padding:8px 12px;font-weight:800;color:var(--text)}

.details-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px}.detail-card{

  display:flex;align-items:center;gap:12px;padding:18px;border-radius:20px;background:var(--card);border:1px solid var(--border);box-shadow:var(--shadow);backdrop-filter:blur(18px)

}.detail-card>span{font-size:1.5rem}.detail-card small{display:block;color:var(--muted);font-weight:700}.detail-card strong{font-size:1.05rem}

.hourly-list{display:flex;gap:12px;overflow-x:auto;padding:3px 2px 12px;scrollbar-width:thin}.hour-card{

  min-width:95px;text-align:center;padding:16px 10px;border-radius:20px;background:var(--card);border:1px solid var(--border);box-shadow:var(--shadow)

}.hour-card.current{outline:2px solid rgba(113,191,240,.55)}.hour-card small{color:var(--muted);font-weight:800}.hour-icon{font-size:1.8rem;margin:10px 0}.hour-temp{font-weight:900}

.daily-list{display:grid;gap:10px}.day-card{

  display:grid;grid-template-columns:1.2fr .8fr 1fr;align-items:center;gap:10px;padding:15px 18px;border-radius:18px;background:var(--card);border:1px solid var(--border);box-shadow:var(--shadow)

}.day-name{font-weight:900}.day-condition{display:flex;align-items:center;gap:8px;color:var(--muted);font-weight:800}.day-temp{text-align:right;font-weight:900}.day-temp span{color:var(--muted);margin-right:10px}

.welcome-card,.error-card{text-align:center;margin:24px auto;padding:46px 24px;max-width:650px;background:var(--card);border:1px solid var(--border);border-radius:30px;box-shadow:var(--shadow);backdrop-filter:blur(20px)}

.welcome-illustration{font-size:4rem;margin-bottom:15px;display:flex;justify-content:center;gap:15px;align-items:center}.welcome-illustration span{font-size:3.5rem}

.welcome-card h1{font-size:clamp(1.7rem,5vw,2.5rem);line-height:1.1}.welcome-card p,.error-card p{color:var(--muted);max-width:480px;margin:0 auto 22px;font-weight:600}

.primary-btn{padding:12px 20px;border-radius:15px}.error-face{font-size:3rem;margin-bottom:8px}

footer{text-align:center;color:var(--muted);font-size:.78rem;font-weight:700;padding:30px 0 4px}

button{transition:transform .2s ease,opacity .2s ease}.icon-btn:hover,.small-btn:hover,.primary-btn:hover,.search-btn:hover{transform:translateY(-2px)}

[hidden]{display:none!important}

@media(max-width:760px){

  .app-shell{width:min(100% - 22px,620px);padding-top:16px}

  .hero-card{padding:27px 22px;min-height:0;flex-direction:column;align-items:stretch;text-align:left}

  .mochi-scene{width:100%;height:220px;order:-1}.weather-orb{right:12%;top:0}.mochi{width:155px;height:130px}

  .details-grid{grid-template-columns:repeat(2,1fr)}.detail-card{padding:15px}

  .day-card{grid-template-columns:1fr auto}.day-condition{justify-self:end}.day-temp{grid-column:1/-1;text-align:left}

  #temperature{font-size:6rem}.temperature-row sup{font-size:1.8rem}

}

@media(max-width:420px){

  .search-btn{padding:11px 13px}.search-box{padding-left:12px}

  .hero-card{border-radius:28px}.details-grid{gap:8px}.detail-card{gap:8px}.detail-card>span{font-size:1.25rem}

  .section{margin-top:24px}.brand{font-size:1rem}

}''',

"script.js": r'''const $ = (id) => document.getElementById(id);

const state = {

  weather: null,

  location: null,

  lastQuery: "",

  dark: localStorage.getItem("mochi-theme") === "dark"

};

const weatherCodes = {

  0: ["Cerah", "☀️", "Mochi lagi ceria!"],

  1: ["Cerah berawan", "🌤️", "Mochi santai di bawah matahari."],

  2: ["Berawan sebagian", "⛅", "Mochi lagi lihat awan."],

  3: ["Berawan", "☁️", "Mochi lagi rebahan di awan."],

  45: ["Berkabut", "🌫️", "Mochi lagi main petak umpet."],

  48: ["Kabut beku", "🌫️", "Mochi butuh jaket nih!"],

  51: ["Gerimis ringan", "🌦️", "Mochi pakai payung kecil."],

  53: ["Gerimis", "🌦️", "Jangan lupa payung ya!"],

  55: ["Gerimis lebat", "🌧️", "Mochi mulai kehujanan."],

  61: ["Hujan ringan", "🌧️", "Mochi pakai jas hujan!"],

  63: ["Hujan", "🌧️", "Waktunya ngadem di rumah."],

  65: ["Hujan lebat", "🌧️", "Mochi jangan lupa payung!"],

  71: ["Salju ringan", "🌨️", "Mochi bikin snowball!"],

  73: ["Salju", "❄️", "Mochi kedinginan."],

  75: ["Salju lebat", "❄️", "Brrr... dingin banget!"],

  80: ["Hujan lokal", "🌦️", "Awan lagi iseng."],

  81: ["Hujan lokal", "🌧️", "Mochi cari tempat teduh."],

  82: ["Hujan sangat lebat", "⛈️", "Mochi buru-buru pulang!"],

  95: ["Badai petir", "⛈️", "Mochi sedikit kaget!"],

  96: ["Petir + hujan es", "⛈️", "Tetap di tempat aman ya."],

  99: ["Badai kuat", "⛈️", "Mochi ngumpet dulu."]

};

function getWeatherInfo(code) {

  return weatherCodes[code] || ["Tidak diketahui", "🌈", "Mochi belum tahu cuacanya."];

}

function formatDate(date, opts) {

  return new Intl.DateTimeFormat("id-ID", opts).format(date);

}

function applyTheme() {

  document.body.classList.toggle("dark", state.dark);

  $("themeBtn").textContent = state.dark ? "☀" : "☾";

  localStorage.setItem("mochi-theme", state.dark ? "dark" : "light");

}

function setStatus(text = "") { $("status").textContent = text; }

function setLoading(loading) {

  if (loading) {

    setStatus("Mochi sedang mengecek langit... ☁️");

    $("welcome").hidden = true;

    $("errorCard").hidden = true;

  } else {

    setStatus("");

  }

}

function showError(message) {

  $("weatherView").hidden = true;

  $("welcome").hidden = true;

  $("errorCard").hidden = false;

  $("errorText").textContent = message;

  setStatus("");

}

async function geocode(city) {

  const url = `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}&count=5&language=id&format=json`;

  const res = await fetch(url);

  if (!res.ok) throw new Error("Geocoding gagal");

  const data = await res.json();

  return data.results || [];

}

function renderSearchResults(results) {

  const box = $("searchResults");

  box.innerHTML = "";

  if (!results.length) {

    box.innerHTML = `<div class="result-btn">Kota tidak ditemukan 🔎</div>`;

  } else {

    results.forEach(place => {

      const btn = document.createElement("button");

      btn.className = "result-btn";

      btn.innerHTML = `<strong>${place.name}</strong><br><small>${place.admin1 || ""}${place.country ? ` · ${place.country}` : ""}</small>`;

      btn.addEventListener("click", () => {

        box.hidden = true;

        $("cityInput").value = place.name;

        loadWeather(place);

      });

      box.appendChild(btn);

    });

  }

  box.hidden = false;

}

async function searchCity() {

  const query = $("cityInput").value.trim();

  if (!query) return;

  setLoading(true);

  try {

    const results = await geocode(query);

    renderSearchResults(results);

    if (results.length === 1) {

      $("searchResults").hidden = true;

      loadWeather(results[0]);

    }

  } catch {

    showError("Pencarian kota gagal. Coba lagi.");

  }

}

async function loadWeather(place) {

  setLoading(true);

  try {

    const params = new URLSearchParams({

      latitude: place.latitude,

      longitude: place.longitude,

      timezone: "auto",

      forecast_days: "7",

      current: "temperature_2m,relative_humidity_2m,apparent_temperature,is_day,precipitation,weather_code,wind_speed_10m",

      hourly: "temperature_2m,weather_code,precipitation_probability",

      daily: "weather_code,temperature_2m_max,temperature_2m_min,precipitation_probability_max"

    });

    const res = await fetch(`https://api.open-meteo.com/v1/forecast?${params}`);

    if (!res.ok) throw new Error("Weather gagal");

    state.weather = await res.json();

    state.location = place;

    state.lastQuery = place.name;

    localStorage.setItem("mochi-location", JSON.stringify(place));

    renderWeather();

  } catch {

    showError("Mochi tidak bisa mengambil data cuaca. Periksa internet lalu coba lagi.");

  }

}

function renderWeather() {

  const w = state.weather, p = state.location;

  const current = w.current;

  const [condition, icon, caption] = getWeatherInfo(current.weather_code);

  $("weatherView").hidden = false;

  $("welcome").hidden = true;

  $("errorCard").hidden = true;

  $("cityName").textContent = p.name;

  $("countryName").textContent = p.country || "Lokasi";

  $("dateText").textContent = formatDate(new Date(), { weekday:"long", day:"numeric", month:"long" });

  $("temperature").textContent = Math.round(current.temperature_2m);

  $("condition").textContent = condition;

  $("feelsLike").textContent = `Terasa seperti ${Math.round(current.apparent_temperature)}°C`;

  $("humidity").textContent = `${current.relative_humidity_2m}%`;

  $("wind").textContent = `${Math.round(current.wind_speed_10m)} km/h`;

  $("feelsDetail").textContent = `${Math.round(current.apparent_temperature)}°C`;

  $("rainChance").textContent = `${w.daily.precipitation_probability_max?.[0] ?? 0}%`;

  $("weatherOrb").textContent = current.is_day ? icon : (current.weather_code === 0 ? "🌙" : icon);

  $("mochiCaption").textContent = caption;

  $("mochi").textContent = current.weather_code >= 95 ? "⊙﹏⊙" : current.weather_code >= 51 ? "•ᴗ•" : "◕ᴗ◕";

  renderHourly();

  renderDaily();

}

function renderHourly() {

  const w = state.weather, now = new Date();

  const start = w.hourly.time.findIndex(t => new Date(t) >= now);

  const first = Math.max(0, start);

  $("hourly").innerHTML = w.hourly.time.slice(first, first + 12).map((time, i) => {

    const idx = first + i;

    const d = new Date(time);

    const [condition, icon] = getWeatherInfo(w.hourly.weather_code[idx]);

    return `<article class="hour-card ${i===0 ? "current" : ""}">

      <small>${i===0 ? "Now" : formatDate(d,{hour:"2-digit",minute:"2-digit"})}</small>

      <div class="hour-icon" title="${condition}">${icon}</div>

      <div class="hour-temp">${Math.round(w.hourly.temperature_2m[idx])}°</div>

    </article>`;

  }).join("");

}

function renderDaily() {

  const w = state.weather;

  $("daily").innerHTML = w.daily.time.map((time, i) => {

    const d = new Date(`${time}T12:00:00`);

    const [condition, icon] = getWeatherInfo(w.daily.weather_code[i]);

    const name = i === 0 ? "Hari ini" : formatDate(d, {weekday:"long"});

    return `<article class="day-card">

      <div class="day-name">${name}</div>

      <div class="day-condition"><span>${icon}</span><span>${condition}</span></div>

      <div class="day-temp"><span>${Math.round(w.daily.temperature_2m_min[i])}°</span>${Math.round(w.daily.temperature_2m_max[i])}°C</div>

    </article>`;

  }).join("");

}

async function useLocation() {

  if (!navigator.geolocation) {

    showError("Browser ini tidak mendukung lokasi.");

    return;

  }

  setLoading(true);

  navigator.geolocation.getCurrentPosition(

    async pos => {

      try {

        const { latitude, longitude } = pos.coords;

        const results = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent("Pamekasan")}&count=1&format=json`).then(r=>r.json());

        const fallback = results.results?.[0] || {name:"Lokasiku",country:"Indonesia"};

        await loadWeather({...fallback, latitude, longitude});

      } catch {

        showError("Lokasi ditemukan, tapi data cuacanya gagal diambil.");

      }

    },

    () => showError("Izin lokasi ditolak. Kamu tetap bisa mencari kota secara manual.")

  );

}

$("searchBtn").addEventListener("click", searchCity);

$("cityInput").addEventListener("keydown", e => {

  if (e.key === "Enter") searchCity();

  if (e.key === "Escape") $("searchResults").hidden = true;

});

$("themeBtn").addEventListener("click", () => { state.dark = !state.dark; applyTheme(); });

$("locationBtn").addEventListener("click", useLocation);

$("refreshBtn").addEventListener("click", () => state.location && loadWeather(state.location));

$("retryBtn").addEventListener("click", () => state.location ? loadWeather(state.location) : searchCity());

document.querySelectorAll("[data-city]").forEach(btn => btn.addEventListener("click", () => {

  $("cityInput").value = btn.dataset.city;

  searchCity();

}));

document.addEventListener("click", e => {

  if (!$("searchResults").contains(e.target) && !$("cityInput").contains(e.target)) $("searchResults").hidden = true;

});

applyTheme();

const saved = localStorage.getItem("mochi-location");

if (saved) {

  try { loadWeather(JSON.parse(saved)); } catch { $("welcome").hidden = false; }

} else {

  $("welcome").hidden = false;

}''',

"manifest.json": r'''{

  "name": "Mochi Weather",

  "short_name": "Mochi Weather",

  "start_url": "./",

  "display": "standalone",

  "background_color": "#edf8ff",

  "theme_color": "#9edcff",

  "description": "Cute weather app powered by Open-Meteo.",

  "icons": [

    {"src":"icon.svg","sizes":"any","type":"image/svg+xml","purpose":"any maskable"}

  ]

}''',

"icon.svg": r'''<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 128 128">

<defs><linearGradient id="g" x1="0" x2="1"><stop stop-color="#b9e7ff"/><stop offset="1" stop-color="#ffe18b"/></linearGradient></defs>

<rect width="128" height="128" rx="32" fill="url(#g)"/>

<circle cx="64" cy="70" r="37" fill="#fff7e8"/>

<circle cx="51" cy="67" r="4" fill="#19324a"/><circle cx="77" cy="67" r="4" fill="#19324a"/>

<path d="M56 80 Q64 87 72 80" fill="none" stroke="#19324a" stroke-width="4" stroke-linecap="round"/>

<circle cx="94" cy="30" r="17" fill="#ffd978"/>

</svg>''',

"README.md": r'''# ☁️ Mochi Weather

Cute responsive weather app built with **HTML + CSS + Vanilla JavaScript**.

## Features

- Search city

- Real-time weather data

- 12-hour forecast

- 7-day forecast

- Weather-based mascot

- Dark mode

- Current location

- LocalStorage

- Responsive mobile/desktop layout

- PWA-ready

- No API key required

## Run locally

Open `index.html` in a browser, or use VS Code Live Server.

## GitHub Pages

1. Create a GitHub repository.

2. Upload all files in this folder.

3. Go to **Settings → Pages**.

4. Select **Deploy from a branch**.

5. Choose `main` and `/root`.

6. Save.

7. Open the generated GitHub Pages URL.

Weather data is provided by Open-Meteo.

'''

}

for name, content in files.items():

    (root / name).write_text(textwrap.dedent(content).strip() + "\n", encoding="utf-8")

zip_path = Path("/mnt/data/mochi-weather-github.zip")

with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as z:

    for p in root.rglob("*"):

        if p.is_file():

            z.write(p, p.relative_to(root))

print(f"Project dibuat: {root}")

print(f"ZIP: {zip_path}")

print("Isi:", ", ".join(files.keys()))
