
<!DOCTYPE html>
 
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-title" content="DVD Builder" />
<title>DVD Catalog Builder</title>
<style>
* { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif; background: #f2f2f7; color: #111; padding: 20px 16px; padding-top: max(20px, env(safe-area-inset-top)); }
h1 { font-size: 22px; font-weight: 700; margin-bottom: 6px; }
.subtitle { font-size: 14px; color: #666; margin-bottom: 20px; line-height: 1.5; }
.card { background: #fff; border-radius: 14px; padding: 18px; margin-bottom: 16px; }
.card-title { font-size: 13px; font-weight: 600; color: #888; text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 12px; }
.progress-wrap { background: #e5e5ea; border-radius: 100px; height: 8px; overflow: hidden; margin: 10px 0; }
.progress-bar { height: 8px; background: #007aff; border-radius: 100px; width: 0%; transition: width 0.3s; }
.status { font-size: 14px; color: #555; margin-bottom: 4px; min-height: 20px; }
.count { font-size: 13px; color: #888; }
.btn { width: 100%; padding: 14px; font-size: 17px; font-weight: 600; border: none; border-radius: 12px; cursor: pointer; margin-top: 10px; -webkit-appearance: none; }
.btn-blue { background: #007aff; color: #fff; }
.btn-blue:disabled { background: #c7c7cc; color: #fff; }
.btn-green { background: #34c759; color: #fff; display: none; }
.movie-list { max-height: 260px; overflow-y: auto; -webkit-overflow-scrolling: touch; margin-top: 10px; }
.movie-item { display: flex; align-items: center; gap: 10px; padding: 6px 0; border-bottom: 1px solid #f2f2f7; font-size: 13px; }
.movie-item img { width: 24px; height: 36px; object-fit: cover; border-radius: 3px; flex-shrink: 0; }
.movie-item .ph { width: 24px; height: 36px; background: #e5e5ea; border-radius: 3px; flex-shrink: 0; }
.tick { color: #34c759; margin-left: auto; flex-shrink: 0; font-size: 14px; }
.cross { color: #ff3b30; margin-left: auto; flex-shrink: 0; font-size: 14px; }
.instructions { background: #fff3cd; border-radius: 14px; padding: 16px; margin-bottom: 16px; font-size: 14px; line-height: 1.6; color: #856404; display: none; }
.instructions b { font-weight: 700; }
.instructions ol { padding-left: 18px; margin-top: 8px; }
.instructions li { margin-bottom: 6px; }
@media (prefers-color-scheme: dark) {
  body { background: #1c1c1e; color: #f0f0f0; }
  .card { background: #2c2c2e; }
  .card-title { color: #888; }
  .progress-wrap { background: #3a3a3c; }
  .status { color: #ccc; }
  .count { color: #888; }
  .movie-item { border-bottom-color: #3a3a3c; }
  .movie-item .ph { background: #3a3a3c; }
  .subtitle { color: #aaa; }
}
</style>
</head>
<body>
 
<h1>DVD Catalog Builder</h1>
<p class="subtitle">Tap Start and keep this page open. Takes about 5 minutes.</p>
 
<div class="instructions" id="instructions">
  <b>How to save your catalog on iPhone:</b>
  <ol>
    <li>Tap <b>Open Catalog</b> below</li>
    <li>In Safari tap the <b>Share icon</b> at the bottom</li>
    <li>Tap <b>Save to Files</b></li>
    <li>Choose a location and tap <b>Save</b></li>
    <li>Open from Files app anytime!</li>
  </ol>
</div>
 
<div class="card">
  <div class="card-title">Progress</div>
  <div class="status" id="status">Ready — tap Start to begin</div>
  <div class="progress-wrap"><div class="progress-bar" id="progress-bar"></div></div>
  <div class="count" id="count">0 / 334 posters</div>
  <button class="btn btn-blue" id="btn-start" onclick="startBuild()">Start Building</button>
  <button class="btn btn-green" id="btn-open" onclick="openCatalog()">Open Catalog</button>
</div>
 
<div class="card">
  <div class="card-title">Progress Log</div>
  <div class="movie-list" id="movie-list"></div>
</div>
 
<script>
const API_KEY = '8670d5dc76f371b2ad77f98e626dfba2';
const IMG_BASE = 'https://image.tmdb.org/t/p/w92';
const SEARCH_URL = 'https://api.themoviedb.org/3/search/movie';
 
const ALL_TITLES = [
"11.22.63","21","21 and Over","22 Jump Street","2012","30 Minutes or Less","50 First Dates",
"A Christmas Story","A Fistful of Dollars","A Million Ways to Die in the West","A Walk Among the Tombstones",
"Abraham Lincoln Vampire Hunter","Ad Astra","After Earth",
"Alien","Alien Covenant","Alien vs Predator","Aliens",
"American Gangster","American History X","American Hustle","American Pie",
"Annihilation","Ant-Man and the Wasp","Apollo 18","Aquaman",
"Austin Powers Goldmember","Avengers Age of Ultron","Avengers Endgame","Avatar",
"Batman vs Superman","Battleship","Baywatch","Bee Movie","Big Trouble in Little China",
"Bill and Ted's Bogus Journey","Bill and Ted's Excellent Adventure",
"Black Panther","Blade Runner","Bleeding Steel","Blended","Bloodshot","Blow","Boondock Saints","Brave","Bumblebee",
"Captain America The First Avenger","Captain America The Winter Soldier",
"Cast Away","Central Intelligence","Chicago","Chips","Civil War","Clash of the Titans",
"Cold Mountain","Collateral","Collateral Damage","Conan the Barbarian","Conan the Destroyer","Constantine","Contraband",
"Dawn of the Dead","Deadpool 2","Deliver Us",
"Despicable Me","Despicable Me 2","Despicable Me 3",
"Die Hard","Die Hard with a Vengeance","Dirty Grandpa","Divergent","Doctor Strange","Dolittle","Dragonheart","Due Date",
"Edward Scissorhands","Enemy of the State","Escape Plan","Eurotrip","Everything Must Go",
"Fantastic Beasts and Where to Find Them","Fantastic Beasts The Crimes of Grindelwald",
"Fast 5","Fast 6","Fast and Furious Hobbs and Shaw","Fast Times at Ridgemont High","Faster",
"Fear and Loathing in Las Vegas","Fight Club","First Man","Full Metal Jacket","Furious 7",
"G.I. Joe","Game Night","Gamer","Get Hard","Ghost in the Shell","Ghost Protocol","Godzilla",
"Gone in 60 Seconds","Goodfellas","Goon","Gravity","Groundhog Day","Grown Ups 2","Guardians of the Galaxy Vol 2",
"Hannibal","Happy Feet","Heartbreak Ridge","Hell Ride","Hercules","Herbie","Hitch","Homefront","Horrible Bosses",
"I Am Legend","Ice Age","Ice Age The Meltdown","Identity Thief","Immortals","Independence Day","Insurgent","Iron Man 3",
"J. Edgar","Jack Reacher","Jack Reacher Never Go Back","Jason Bourne","Jayne Mansfield's Car",
"Jeepers Creepers","Joe Dirt","John Wick","John Wick 2","John Wick 3","Jonah Hex",
"Jumanji","Jumanji Welcome to the Jungle","Jurassic World","Just Friends","Justice League","JW Fallen Kingdom",
"Kick-Ass","Kick-Ass 2","Kill Bill Vol 1","Kill Bill Vol 2","Kingsman Secret Service","Kiss the Girls",
"Last Knights","Leatherheads","Lethal Weapon","Lethal Weapon 2","Lethal Weapon 3","Lethal Weapon 4",
"Little Nicky","Logan","Lone Survivor",
"Mad Max Fury Road","Masterminds","Mechanic Resurrection","Men in Black","Million Dollar Baby",
"Mission Impossible","Mission Impossible Fallout","Mission Impossible Rogue Nation",
"Mr and Mrs Smith","Mrs Doubtfire","My Cousin Vinny",
"Nacho Libre","National Lampoons Christmas Vacation","Natural Born Killers","Need for Speed","Next","Nine Months",
"Oblivion","Oceans Eleven","Once Upon a Time in Venice","Orange County","Our Family Wedding","Out of the Furnace","Outbreak",
"Pacific Rim","Pacific Rim Uprising","Pain and Gain","Passengers",
"Pirates of the Caribbean Dead Men Tell No Tales","Pirates of the Caribbean On Stranger Tides",
"Point Break","Pompeii","Predator","Predators","Priest","Psych 9",
"Rain Man","Rambo","Rambo Last Blood","Rampage","Rampart","Ready Player One",
"Reservoir Dogs","Revenge of the Nerds","Ride Along","Ride Along 2","Risky Business","Road House","Robin Hood","Rogue One",
"Rush Hour","Rush Hour 2",
"Safe House","Savages","Scarface","School of Rock","Seventh Son","Sex Tape","Shallow Hal",
"Shanghai Knights","Shanghai Noon","Sherlock Holmes A Game of Shadows",
"Snow White and the Huntsman","Solo A Star Wars Story","Spaceballs","Speed",
"Star Wars The Force Awakens","Star Wars The Last Jedi",
"Super 8","Super Troopers","Super Troopers 2","Survival of the Dead",
"Tag","Taken","Talladega Nights","Tammy","Ted","Tenet","Terminator Genisys","Texas Chainsaw Massacre",
"The A-Team","The Accountant","The Aviator","The Breakfast Club","The Bucket List","The Client",
"The Dark Knight Rises","The Day After Tomorrow","The Dead Girl","The Dukes of Hazard",
"The Equalizer","The Expendables 3","The Fast and the Furious Tokyo Drift","The Fate of the Furious",
"The Fighter","The Gambler","The Gentlemen","The Glass House","The Green Mile","The Grinch",
"The Hangover","The Hangover Part 2","The Hangover Part 3","The Heat","The House Bunny",
"The Hunger Games","The Hunger Games Catching Fire","The Hunger Games Mockingjay","The Hunger Games Mockingjay Pt 2",
"The Hungover Games","The Ideas of March","The Karate Kid","The Lego Batman Movie",
"The Lorax","The Losers","The Magnificent Seven","The Martian","The Mechanic","The Meg",
"The Monuments Men","The Mule","The Musketeer","The Never-ending Story","The Nice Guys",
"The Outlaw","The Outsiders","The Quest","The Quick and the Dead","The Rundown",
"The Shawshank Redemption","The Shining","The Silence of the Lambs","The Son of No One",
"The Sorcerer's Apprentice","The Tax Collector","The Terminator","The Thing","The Town",
"The War with Grandpa","The Wolf of Wall Street","The Wolverine","The Woodsman","The World's End","The Wrestler","The Zodiac",
"Thelma and Louise","This is the End","Thor Ragnarok","Three Kings","Tinker Tailor Soldier Spy",
"Tomb Raider","Tower Heist","Transcendence","Transformers","Transformers The Last Knight",
"Trouble with the Curve","Tropic Thunder","Troy","Twister",
"Unforgiven","Vacation",
"War Dogs","Warrior","We're the Millers","Weekend at Bernie's","When Harry Met Sally",
"White Men Can't Jump","Why Him","Wild Card","Wonder Woman","World War Z","Wyatt Earp",
"X-Men","X-Men Apocalypse","X-Men Last Stand","X-Men Origins Wolverine","X2",
"Yogi Bear","Young Guns","Zodiac"
].sort((a,b) => {
  const clean = s => s.replace(/^(The|A|An) /i,'').toLowerCase();
  return clean(a).localeCompare(clean(b));
});
 
let posterData = {};
let catalogHTML = '';
 
async function imgToBase64(url) {
  try {
    const resp = await fetch(url);
    const blob = await resp.blob();
    return await new Promise((res, rej) => {
      const reader = new FileReader();
      reader.onloadend = () => res(reader.result);
      reader.onerror = rej;
      reader.readAsDataURL(blob);
    });
  } catch(e) { return null; }
}
 
async function searchTitle(title) {
  const query = title.replace(/^(The|A|An) /i,'').replace(/[^a-zA-Z0-9 ]/g,'').trim();
  try {
    const r = await fetch(`${SEARCH_URL}?api_key=${API_KEY}&query=${encodeURIComponent(query)}&language=en-US&page=1`);
    const data = await r.json();
    if (data.results && data.results.length > 0 && data.results[0].poster_path) {
      return IMG_BASE + data.results[0].poster_path;
    }
  } catch(e) {}
  return null;
}
 
async function startBuild() {
  document.getElementById('btn-start').disabled = true;
  document.getElementById('btn-start').textContent = 'Building...';
  const total = ALL_TITLES.length;
  let done = 0;
  const listEl = document.getElementById('movie-list');
 
  const BATCH = 3;
  for (let i = 0; i < ALL_TITLES.length; i += BATCH) {
    const batch = ALL_TITLES.slice(i, i + BATCH);
    await Promise.all(batch.map(async title => {
      const posterUrl = await searchTitle(title);
      let b64 = null;
      if (posterUrl) b64 = await imgToBase64(posterUrl);
      posterData[title] = b64;
      done++;
 
      const pct = Math.round((done / total) * 100);
      document.getElementById('progress-bar').style.width = pct + '%';
      document.getElementById('count').textContent = `${done} / ${total} posters`;
      document.getElementById('status').textContent = title;
 
      const item = document.createElement('div');
      item.className = 'movie-item';
      if (b64) {
        item.innerHTML = `<img src="${b64}" alt="" /><span>${title}</span><span class="tick">✓</span>`;
      } else {
        item.innerHTML = `<div class="ph"></div><span>${title}</span><span class="cross">—</span>`;
      }
      listEl.appendChild(item);
      listEl.scrollTop = listEl.scrollHeight;
    }));
    await new Promise(r => setTimeout(r, 200));
  }
 
  document.getElementById('status').textContent = 'All done!';
  buildCatalogHTML();
  document.getElementById('btn-open').style.display = 'block';
  document.getElementById('instructions').style.display = 'block';
  document.getElementById('btn-start').textContent = 'Complete';
}
 
function buildCatalogHTML() {
  let num = 1;
  const dataJson = JSON.stringify(ALL_TITLES.map(t => ({ title: t, poster: posterData[t] || null, num: num++ })));
 
  catalogHTML = `<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0"/>
<meta name="apple-mobile-web-app-capable" content="yes"/>
<meta name="apple-mobile-web-app-title" content="My DVDs"/>
<title>My DVD Collection</title>
<style>
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif;background:#f2f2f7;color:#111}
header{background:#fff;border-bottom:1px solid #e0e0e0;padding:12px 16px 10px;position:sticky;top:0;z-index:10}
header h1{font-size:17px;font-weight:700;margin-bottom:8px}
#search{width:100%;padding:9px 14px;font-size:16px;border:1px solid #ddd;border-radius:10px;background:#f2f2f7;color:#111;-webkit-appearance:none}
#search:focus{outline:none;border-color:#007aff;background:#fff}
#stats{font-size:12px;color:#999;margin-top:6px}
.sh{font-size:12px;font-weight:700;color:#888;letter-spacing:.08em;text-transform:uppercase;padding:8px 16px 4px;border-bottom:1px solid #e8e8e8;background:#f2f2f7;position:sticky;top:89px;z-index:5}
.mr{display:flex;align-items:center;gap:12px;padding:8px 16px;border-bottom:1px solid #f0f0f0;background:#fff;min-height:68px}
.mr:active{background:#f5f5f5}
.num{font-size:11px;color:#c0c0c0;width:26px;text-align:right;flex-shrink:0}
.pt{width:40px;height:60px;border-radius:5px;background:#e8e8e8;display:flex;align-items:center;justify-content:center;flex-shrink:0;overflow:hidden;box-shadow:0 1px 3px rgba(0,0,0,.15)}
.pt img{width:40px;height:60px;object-fit:cover;border-radius:5px;display:block}
.pp{font-size:8px;color:#bbb;text-align:center;line-height:1.3;padding:3px;font-weight:700}
.ti{font-size:15px;color:#111;flex:1;line-height:1.3}
.nr{padding:60px 16px;text-align:center;color:#aaa;font-size:15px}
@media(prefers-color-scheme:dark){
body{background:#1c1c1e;color:#f0f0f0}
header{background:#2c2c2e;border-bottom-color:#3a3a3c}
#search{background:#3a3a3c;border-color:#3a3a3c;color:#f0f0f0}
#search:focus{background:#48484a;border-color:#007aff}
.sh{background:#1c1c1e;color:#888;border-bottom-color:#2c2c2e}
.mr{background:#2c2c2e;border-bottom-color:#3a3a3c}
.mr:active{background:#3a3a3c}
.ti{color:#f0f0f0}
.pt{background:#3a3a3c}}
</style>
 
</head>
<body>
<header>
<h1>My DVD Collection</h1>
<input type="search" id="search" placeholder="Search..." oninput="f()" autocomplete="off" autocorrect="off" autocapitalize="off" spellcheck="false"/>
<div id="stats"></div>
</header>
<div id="list"></div>
<script>
const D=${dataJson};
let fl=[...D];
function lg(t){const c=t.replace(/^(The|A|An) /i,'')[0].toUpperCase();return/[0-9]/.test(c)?'#':c;}
function render(l){
const el=document.getElementById('list');
if(!l.length){el.innerHTML='<div class="nr">No titles found</div>';return;}
let h='',ll='';
l.forEach(item=>{
const g=lg(item.title);
if(g!==ll){h+='<div class="sh">'+g+'</div>';ll=g;}
const ab=item.title.replace(/^(The|A|An) /i,'').substring(0,3).toUpperCase();
const im=item.poster?'<img src="'+item.poster+'" alt="" />':'<div class="pp">'+ab+'</div>';
h+='<div class="mr"><span class="num">'+item.num+'</span><div class="pt">'+im+'</div><span class="ti">'+item.title+'</span></div>';
});
el.innerHTML=h;
document.getElementById('stats').textContent='Showing '+l.length+' of '+D.length+' titles';
}
function f(){
const q=document.getElementById('search').value.toLowerCase();
fl=q?D.filter(d=>d.title.toLowerCase().includes(q)):[...D];
render(fl);
}
render(D);
document.getElementById('stats').textContent=D.length+' titles total';
<\/script>
</body>
</html>`;
}
 
function openCatalog() {
const blob = new Blob([catalogHTML], { type: ‘text/html’ });
const url = URL.createObjectURL(blob);
window.open(url, ‘_blank’);
}
</script>
 
</body>
</html>
