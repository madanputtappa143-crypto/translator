# translator
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Dhwani — Kannada · English · Hindi Voice Translator</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,600&family=Inter:wght@400;500;600&family=Noto+Sans+Kannada:wght@400;600&family=Noto+Sans+Devanagari:wght@400;600&display=swap');

  :root {
    --ink: #1C1B22;
    --paper: #FBF8F2;
    --marigold: #D98E28;
    --indigo: #2C3454;
    --line: #E4DDCE;
  }

  * { box-sizing: border-box; }

  body {
    margin: 0;
    background: var(--paper);
    color: var(--ink);
    font-family: 'Inter', sans-serif;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    padding: 48px 20px;
  }

  .app { width: 100%; max-width: 640px; }

  header { margin-bottom: 28px; }

  h1 {
    font-family: 'Fraunces', serif;
    font-weight: 600;
    font-size: 40px;
    margin: 0 0 6px 0;
    letter-spacing: -0.01em;
  }

  header p { margin: 0; color: #6B6459; font-size: 15px; max-width: 46ch; }

  .lang-picker {
    display: flex;
    gap: 8px;
    margin-bottom: 24px;
    flex-wrap: wrap;
  }

  .lang-chip {
    border: 1px solid var(--line);
    background: white;
    border-radius: 999px;
    padding: 8px 16px;
    font-size: 13px;
    font-weight: 500;
    cursor: pointer;
    color: var(--ink);
  }
  .lang-chip.active {
    background: var(--indigo);
    border-color: var(--indigo);
    color: white;
  }
  .picker-label {
    font-size: 12px;
    color: #6B6459;
    width: 100%;
    margin-bottom: 2px;
  }

  .stage {
    background: var(--ink);
    border-radius: 20px;
    padding: 36px 28px;
    position: relative;
    overflow: hidden;
  }

  .mic-row {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 18px;
  }

  .mic-btn {
    width: 84px;
    height: 84px;
    border-radius: 50%;
    border: none;
    background: var(--marigold);
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: transform 0.15s ease, background 0.15s ease;
    flex-shrink: 0;
  }
  .mic-btn:hover { transform: scale(1.04); }
  .mic-btn:active { transform: scale(0.97); }
  .mic-btn.listening { background: #C94F3D; animation: pulse 1.4s infinite; }
  .mic-btn:disabled { opacity: 0.5; cursor: not-allowed; }

  @keyframes pulse {
    0% { box-shadow: 0 0 0 0 rgba(201,79,61,0.5); }
    70% { box-shadow: 0 0 0 18px rgba(201,79,61,0); }
    100% { box-shadow: 0 0 0 0 rgba(201,79,61,0); }
  }

  .mic-btn svg { width: 30px; height: 30px; }

  .status { color: #C9C4B8; font-size: 13px; min-height: 18px; text-align: center; }
  .status.live { color: #E8A96E; }

  .waveform {
    display: flex; align-items: center; justify-content: center; gap: 4px; height: 28px;
  }
  .waveform span {
    width: 3px; background: var(--marigold); border-radius: 2px; height: 6px;
    transition: height 0.15s ease;
  }

  .src-row {
    display: flex;
    align-items: flex-start;
    gap: 8px;
    margin-top: 22px;
  }
  .src-text {
    color: var(--paper);
    font-size: 19px;
    line-height: 1.6;
    text-align: left;
    min-height: 30px;
    flex: 1;
  }

  .copy-btn {
    background: none;
    border: 1px solid #3A3946;
    border-radius: 8px;
    width: 30px;
    height: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    color: var(--paper);
    flex-shrink: 0;
  }
  .copy-btn:hover { background: #29283355; }
  .copy-btn svg { width: 14px; height: 14px; }
  .copy-btn.copied { color: var(--marigold); border-color: var(--marigold); }

  .type-row {
    display: flex;
    gap: 8px;
    margin-top: 18px;
  }
  .type-row input {
    flex: 1;
    background: #29283333;
    border: 1px solid #3A3946;
    border-radius: 10px;
    padding: 10px 14px;
    color: var(--paper);
    font-size: 15px;
    font-family: inherit;
  }
  .type-row input::placeholder { color: #8B899A; }
  .type-row input:focus { outline: none; border-color: var(--marigold); }
  .type-row button {
    background: var(--marigold);
    border: none;
    border-radius: 10px;
    padding: 0 20px;
    color: var(--ink);
    font-weight: 600;
    font-size: 14px;
    cursor: pointer;
  }
  .type-row button:hover { background: #E8A03A; }

  .results { margin-top: 22px; display: grid; gap: 14px; }

  .card {
    background: var(--paper);
    border: 1px solid var(--line);
    border-radius: 14px;
    padding: 18px 20px;
  }
  .card-head {
    display: flex; align-items: center; justify-content: space-between; margin-bottom: 8px;
  }
  .card-actions { display: flex; gap: 6px; }
  .lang-label { font-size: 12px; font-weight: 600; color: var(--indigo); }

  .card .copy-btn, .card .speak-btn {
    background: none; border: 1px solid var(--line); border-radius: 8px;
    width: 32px; height: 32px; display: flex; align-items: center; justify-content: center;
    cursor: pointer; color: var(--indigo);
  }
  .card .copy-btn:hover, .card .speak-btn:hover { background: #F1ECE0; }
  .card .copy-btn svg, .card .speak-btn svg { width: 16px; height: 16px; }
  .card .copy-btn.copied { color: var(--marigold); border-color: var(--marigold); }
  .voice-note { font-size: 11px; color: #A39C8D; margin-top: 6px; }

  .out-text { font-size: 17px; line-height: 1.55; }
  .out-text.kn { font-family: 'Noto Sans Kannada', sans-serif; font-size: 18px; }
  .out-text.hi { font-family: 'Noto Sans Devanagari', sans-serif; font-size: 18px; }

  .unsupported {
    background: #F6E4DE; border: 1px solid #E3B3A3; color: #8A3B26;
    border-radius: 12px; padding: 14px 16px; font-size: 14px; margin-bottom: 20px;
  }

  footer { margin-top: 28px; text-align: center; font-size: 12px; color: #A39C8D; }
</style>
</head>
<body>
<div class="app">
  <header>
    <h1>Dhwani</h1>
    <p>Speak in Kannada, English, or Hindi — get it translated into the other two, as text and speech.</p>
  </header>

  <div id="unsupportedBox" class="unsupported" style="display:none;">
    Voice input isn't supported in this browser. Try Chrome or Edge on desktop.
  </div>

  <div class="lang-picker">
    <div class="picker-label">I'm speaking in:</div>
    <button class="lang-chip active" data-lang="kn">ಕನ್ನಡ Kannada</button>
    <button class="lang-chip" data-lang="en">English</button>
    <button class="lang-chip" data-lang="hi">हिन्दी Hindi</button>
  </div>

  <div class="stage">
    <div class="mic-row">
      <button id="micBtn" class="mic-btn" aria-label="Start speaking">
        <svg viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2" stroke-linecap="round">
          <path d="M12 15a3 3 0 0 0 3-3V6a3 3 0 0 0-6 0v6a3 3 0 0 0 3 3z"/>
          <path d="M19 11a7 7 0 0 1-14 0"/>
          <line x1="12" y1="18" x2="12" y2="22"/>
        </svg>
      </button>
      <div id="waveform" class="waveform" style="display:none;">
        <span></span><span></span><span></span><span></span><span></span><span></span><span></span>
      </div>
      <div id="status" class="status">Tap the mic and speak</div>
    </div>
    <div class="src-row">
      <div id="srcText" class="src-text kn placeholder">What you say will appear here</div>
      <button id="speakSrcBtn" class="copy-btn" aria-label="Play what you said" style="display:none;">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M11 5 6 9H2v6h4l5 4V5z"/><path d="M19.07 4.93a10 10 0 0 1 0 14.14M15.54 8.46a5 5 0 0 1 0 7.07"/></svg>
      </button>
      <button id="copySrcBtn" class="copy-btn" aria-label="Copy text" style="display:none;">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="9" y="9" width="13" height="13" rx="2"/><path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/></svg>
      </button>
    </div>

    <div class="type-row">
      <input id="typeInput" type="text" placeholder="…or type it here instead" />
      <button id="typeBtn" aria-label="Translate typed text">Go</button>
    </div>

    <div id="results" class="results" style="display:none;"></div>
  </div>

  <footer>Speech recognition and playback run in your browser. Nothing is stored.</footer>
</div>

<script>
const LANGS = {
  kn: { name: 'ಕನ್ನಡ / Kannada', speechLocale: 'kn-IN', ttsLocale: 'kn-IN', fontClass: 'kn' },
  en: { name: 'English',          speechLocale: 'en-US', ttsLocale: 'en-US', fontClass: '' },
  hi: { name: 'हिन्दी / Hindi',    speechLocale: 'hi-IN', ttsLocale: 'hi-IN', fontClass: 'hi' }
};

let sourceLang = 'kn';
let translations = {};
let voices = [];

const micBtn = document.getElementById('micBtn');
const statusEl = document.getElementById('status');
const waveform = document.getElementById('waveform');
const srcText = document.getElementById('srcText');
const results = document.getElementById('results');
const unsupportedBox = document.getElementById('unsupportedBox');
const chips = document.querySelectorAll('.lang-chip');

chips.forEach(chip => {
  chip.addEventListener('click', () => {
    chips.forEach(c => c.classList.remove('active'));
    chip.classList.add('active');
    sourceLang = chip.dataset.lang;
    srcText.className = 'src-text placeholder ' + LANGS[sourceLang].fontClass;
    srcText.textContent = 'What you say will appear here';
    copySrcBtn.style.display = 'none';
    speakSrcBtn.style.display = 'none';
    results.style.display = 'none';
    results.innerHTML = '';
    if (recognition) recognition.lang = LANGS[sourceLang].speechLocale;
  });
});

function loadVoices() {
  voices = window.speechSynthesis ? window.speechSynthesis.getVoices() : [];
}
if (window.speechSynthesis) {
  loadVoices();
  window.speechSynthesis.onvoiceschanged = loadVoices;
}

let waveInterval = null;
function startWaveAnim() {
  const bars = waveform.querySelectorAll('span');
  waveInterval = setInterval(() => {
    bars.forEach(b => { b.style.height = (6 + Math.random() * 20) + 'px'; });
  }, 150);
}
function stopWaveAnim() { clearInterval(waveInterval); }

const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
let recognition;
if (!SpeechRecognition) {
  unsupportedBox.style.display = 'block';
  micBtn.disabled = true;
} else {
  recognition = new SpeechRecognition();
  recognition.lang = LANGS[sourceLang].speechLocale;
  recognition.interimResults = true;
  recognition.maxAlternatives = 1;

  recognition.onstart = () => {
    micBtn.classList.add('listening');
    waveform.style.display = 'flex';
    statusEl.textContent = 'Listening…';
    statusEl.classList.add('live');
    startWaveAnim();
  };

  recognition.onresult = (event) => {
    let transcript = '';
    for (let i = 0; i < event.results.length; i++) transcript += event.results[i][0].transcript;
    srcText.textContent = transcript;
    srcText.classList.remove('placeholder');
    copySrcBtn.style.display = 'flex';
    speakSrcBtn.style.display = 'flex';
  };

  recognition.onerror = (event) => {
    statusEl.textContent = 'Mic error: ' + event.error + '. Tap to try again.';
    statusEl.classList.remove('live');
  };

  recognition.onend = () => {
    micBtn.classList.remove('listening');
    waveform.style.display = 'none';
    stopWaveAnim();
    const finalText = srcText.textContent.trim();
    if (finalText && finalText !== 'What you say will appear here') {
      statusEl.textContent = 'Translating…';
      translate(finalText);
    } else {
      statusEl.textContent = 'Tap the mic and speak';
      statusEl.classList.remove('live');
    }
  };
}

const typeInput = document.getElementById('typeInput');
const typeBtn = document.getElementById('typeBtn');

async function copyText(text, btn) {
  try {
    await navigator.clipboard.writeText(text);
  } catch (e) {
    const ta = document.createElement('textarea');
    ta.value = text;
    document.body.appendChild(ta);
    ta.select();
    document.execCommand('copy');
    document.body.removeChild(ta);
  }
  btn.classList.add('copied');
  setTimeout(() => btn.classList.remove('copied'), 1200);
}

const copySrcBtn = document.getElementById('copySrcBtn');
copySrcBtn.addEventListener('click', () => copyText(srcText.textContent, copySrcBtn));

const speakSrcBtn = document.getElementById('speakSrcBtn');
speakSrcBtn.addEventListener('click', () => speakRaw(srcText.textContent, sourceLang, LANGS[sourceLang].ttsLocale));

function submitTyped() {
  const text = typeInput.value.trim();
  if (!text) return;
  srcText.textContent = text;
  srcText.classList.remove('placeholder');
  copySrcBtn.style.display = 'flex';
  speakSrcBtn.style.display = 'flex';
  results.style.display = 'none';
  results.innerHTML = '';
  statusEl.textContent = 'Translating…';
  translate(text);
}

typeBtn.addEventListener('click', submitTyped);
typeInput.addEventListener('keydown', (e) => {
  if (e.key === 'Enter') submitTyped();
});

micBtn.addEventListener('click', () => {
  if (!recognition) return;
  if (micBtn.classList.contains('listening')) {
    recognition.stop();
  } else {
    srcText.textContent = '';
    srcText.classList.add('placeholder');
    results.style.display = 'none';
    results.innerHTML = '';
    recognition.lang = LANGS[sourceLang].speechLocale;
    recognition.start();
  }
});

async function translateOne(text, sourceLang, targetLang) {
  const url = 'https://translate.googleapis.com/translate_a/single?client=gtx&sl='
    + sourceLang + '&tl=' + targetLang + '&dt=t&q=' + encodeURIComponent(text);
  const res = await fetch(url);
  if (!res.ok) throw new Error('HTTP ' + res.status);
  const data = await res.json();
  return data[0].map(seg => seg[0]).join('');
}

async function translate(text) {
  const targets = Object.keys(LANGS).filter(l => l !== sourceLang);
  try {
    const outputs = await Promise.all(targets.map(t => translateOne(text, sourceLang, t)));
    translations = {};
    results.innerHTML = '';
    targets.forEach((t, i) => {
      translations[t] = outputs[i];
      const card = document.createElement('div');
      card.className = 'card';
      card.innerHTML = `
        <div class="card-head">
          <span class="lang-label">${LANGS[t].name.toUpperCase()}</span>
          <div class="card-actions">
            <button class="copy-btn" data-copy="${t}" aria-label="Copy ${LANGS[t].name}">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="9" y="9" width="13" height="13" rx="2"/><path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/></svg>
            </button>
            <button class="speak-btn" data-lang="${t}" aria-label="Play ${LANGS[t].name}">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M11 5 6 9H2v6h4l5 4V5z"/><path d="M19.07 4.93a10 10 0 0 1 0 14.14M15.54 8.46a5 5 0 0 1 0 7.07"/></svg>
            </button>
          </div>
        </div>
        <div class="out-text ${LANGS[t].fontClass}">${outputs[i]}</div>
        <div class="voice-note" data-voice-note="${t}"></div>
      `;
      results.appendChild(card);
    });
    results.querySelectorAll('.speak-btn').forEach(btn => {
      btn.addEventListener('click', () => speakText(btn.dataset.lang));
    });
    results.querySelectorAll('.copy-btn[data-copy]').forEach(btn => {
      btn.addEventListener('click', () => copyText(translations[btn.dataset.copy], btn));
    });
    results.style.display = 'grid';
    statusEl.textContent = 'Tap the mic and speak';
    statusEl.classList.remove('live');
  } catch (err) {
    console.error(err);
    statusEl.textContent = 'Translation failed: ' + err.message;
    statusEl.classList.remove('live');
  }
}

function pickVoice(locale) {
  if (!voices.length) loadVoices();
  const exact = voices.find(v => v.lang === locale);
  if (exact) return exact;
  const prefix = locale.split('-')[0];
  const loose = voices.find(v => v.lang && v.lang.startsWith(prefix));
  return loose || null;
}

function chunkText(text, maxLen) {
  const words = text.split(' ');
  const chunks = [];
  let current = '';
  for (const w of words) {
    if ((current + ' ' + w).trim().length > maxLen) {
      if (current) chunks.push(current.trim());
      current = w;
    } else {
      current = (current + ' ' + w).trim();
    }
  }
  if (current) chunks.push(current.trim());
  return chunks.length ? chunks : [text];
}

let currentAudio = null;

function playRealVoice(text, langCode) {
  return new Promise((resolve, reject) => {
    if (!text) { resolve(); return; }
    const chunks = chunkText(text, 190);
    const audio = new Audio();
    currentAudio = audio;
    let i = 0;
    audio.onerror = () => reject(new Error('audio unavailable'));
    audio.onended = playNext;
    function playNext() {
      if (i >= chunks.length) { resolve(); return; }
      const url = 'https://translate.google.com/translate_tts?ie=UTF-8&client=tw-ob&tl='
        + langCode + '&q=' + encodeURIComponent(chunks[i]);
      i++;
      audio.src = url;
      audio.play().catch(reject);
    }
    playNext();
  });
}

function playBrowserVoice(text, locale) {
  if (!text || !window.speechSynthesis) return;
  const utter = new SpeechSynthesisUtterance(text);
  const voice = pickVoice(locale);
  if (voice) {
    utter.voice = voice;
    utter.lang = voice.lang;
  } else {
    utter.lang = locale;
  }
  window.speechSynthesis.cancel();
  window.speechSynthesis.speak(utter);
}

async function speakRaw(text, langCode, browserLocale) {
  if (!text) return;
  if (currentAudio) { currentAudio.pause(); currentAudio = null; }
  window.speechSynthesis && window.speechSynthesis.cancel();
  try {
    await playRealVoice(text, langCode);
  } catch (e) {
    playBrowserVoice(text, browserLocale);
  }
}

function speakText(lang) {
  speakRaw(translations[lang], lang, LANGS[lang].ttsLocale);
}
</script>
</body>
</html>
