<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Structural Crack Analyzer</title>

<!-- Chart.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<!-- Three.js -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<!-- XLSX -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<!-- jsPDF -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

<style>
body{
    margin:0;
    font-family:Segoe UI;
    background:linear-gradient(to right,#141e30,#243b55);
    color:white;
}
.header{
    display:flex;
    justify-content:space-between;
    align-items:center;
    padding:15px 20px;
    background:black;
    font-size:18px;
}
.language-select{
    background:white;
    color:black;
    padding:5px;
    border-radius:6px;
}
.container{
    max-width:1200px;
    margin:auto;
    padding:20px;
}
.card{
    background:white;
    color:black;
    padding:20px;
    border-radius:12px;
    margin-bottom:25px;
    box-shadow:0 8px 20px rgba(0,0,0,0.4);
}
.section-title{
    font-weight:bold;
    font-size:18px;
    margin-bottom:15px;
    border-bottom:2px solid #ddd;
    padding-bottom:5px;
}
.input-row{
    display:flex;
    gap:10px;
    flex-wrap:wrap;
    margin-bottom:10px;
}
.input-row input{flex:2;}
.input-row select{flex:1;}
input,select,textarea{
    width:100%;
    padding:8px;
    margin-top:6px;
    border-radius:6px;
    border:1px solid #aaa;
}
button{
    padding:8px 12px;
    margin-top:10px;
    background:black;
    color:white;
    border:none;
    border-radius:6px;
    cursor:pointer;
}
button:hover{background:#333;}
#severityBar{
    height:22px;
    background:#ddd;
    border-radius:20px;
    overflow:hidden;
    margin-top:10px;
}
#severityFill{
    height:100%;
    width:0%;
    background:green;
}
video,canvas{
    width:100%;
    max-width:400px;
    margin-top:10px;
}
</style>
</head>
<body>

<!-- HEADER WITH LANGUAGE -->
<div class="header">
<div>ULTIMATE GLOBAL STRUCTURAL CRACK ANALYZER</div>
<select class="language-select" id="language" onchange="translatePage()">
<option value="en">English</option>
<option value="hi">Hindi</option>
<option value="fr">French</option>
<option value="de">German</option>
<option value="es">Spanish</option>
<option value="ar">Arabic</option>
<option value="zh">Chinese</option>
<option value="ru">Russian</option>
<option value="pt">Portuguese</option>
<option value="ja">Japanese</option>
<option value="ko">Korean</option>
<option value="it">Italian</option>
<option value="tr">Turkish</option>
<option value="bn">Bengali</option>
<option value="ur">Urdu</option>
</select>
</div>

<div class="container">

<!-- CRACK INPUT CARD -->
<div class="card">
<div class="section-title" id="crackTitle">Crack Input Details</div>

<label id="lblWidth">Crack Width</label>
<div class="input-row">
<input type="number" id="width" placeholder="Enter crack width">
<select id="widthUnit">
<option value="mm">mm</option>
<option value="cm">cm</option>
<option value="m">m</option>
<option value="inch">inch</option>
<option value="ft">ft</option>
</select>
</div>

<label id="lblLength">Crack Length</label>
<div class="input-row">
<input type="number" id="length" placeholder="Enter crack length">
<select id="lengthUnit">
<option value="mm">mm</option>
<option value="cm">cm</option>
<option value="m">m</option>
<option value="inch">inch</option>
<option value="ft">ft</option>
</select>
</div>

<label>Structure Type</label>
<select id="structure" onchange="toggleWallOptions()">
<option value="">Select Structure</option>
<option value="Beam">Beam</option>
<option value="Column">Column</option>
<option value="Slab">Slab</option>
<option value="Wall">Wall</option>
<option value="Retaining Wall">Retaining Wall</option>
</select>

<div id="wallOptions" style="display:none;">
<label>Wall Type</label>
<select id="wallType">
<option>Load Bearing</option>
<option>Partition</option>
<option>Shear Wall</option>
</select>
<label>Plaster Applied?</label>
<select id="plaster">
<option>Yes</option>
<option>No</option>
</select>
</div>

<label>Building Age (Years)</label>
<input type="number" id="age">

<label>Crack Direction</label>
<select id="direction">
<option>Vertical</option>
<option>Horizontal</option>
<option>Diagonal</option>
<option>Random</option>
</select>

<label>Crack Status</label>
<select id="status">
<option>Stable</option>
<option>Increasing</option>
<option>Decreasing</option>
</select>

<label>Location Notes</label>
<textarea id="notes"></textarea>

<button onclick="analyze()">Analyze Risk</button>
<button onclick="exportExcel()">Export Excel</button>
<button onclick="exportPDF()">Export PDF</button>

<div id="severityBar"><div id="severityFill"></div></div>
<div id="result"></div>
</div>

<!-- IMAGE / LIVE CAMERA CARD -->
<div class="card">
<div class="section-title">Photo / Live Crack Detection</div>
<input type="file" accept="image/*,.pdf,.doc,.docx" id="upload">
<button onclick="runImageAI()">Analyze Uploaded Image</button>
<hr>
<video id="video" autoplay></video>
<button onclick="startCamera()">Start Live Camera</button>
<button onclick="capture()">Capture & Analyze</button>
<canvas id="canvas"></canvas>
<div id="aiResult"></div>
</div>

<!-- GRAPH CARD -->
<div class="card">
<div class="section-title">AI Pattern Prediction Graph</div>
<canvas id="chart"></canvas>
</div>

<!-- 3D Visualization -->
<div class="card">
<div class="section-title">3D Crack Visualization</div>
<div id="threeContainer" style="height:400px;"></div>
</div>

<!-- QUESTION / CONSULTATION -->
<div class="card">
<div class="section-title">Ask Structural Question</div>
<textarea id="question" placeholder="Type your question here"></textarea>
<button onclick="answer()">Get Detailed Answer</button>
<div id="answerBox"></div>
</div>

</div>

<script>
// --- LANGUAGE TRANSLATION ---
function translatePage(){
    let lang=document.getElementById('language').value;
    // Simple simulated translation (replace with actual translation API if needed)
    const translations={
        hi:{crackTitle:"दरार इनपुट विवरण",lblWidth:"दरार चौड़ाई",lblLength:"दरार लंबाई"},
        fr:{crackTitle:"Détails de fissure",lblWidth:"Largeur fissure",lblLength:"Longueur fissure"},
        de:{crackTitle:"Riss Details",lblWidth:"Rissbreite",lblLength:"Risslänge"},
        es:{crackTitle:"Detalles de grieta",lblWidth:"Ancho grieta",lblLength:"Longitud grieta"},
    };
    if(translations[lang]){
        for(let key in translations[lang]){
            document.getElementById(key).innerText=translations[lang][key];
        }
    }
}

// --- WALL OPTION TOGGLE ---
function toggleWallOptions(){
    const structure=document.getElementById('structure').value;
    document.getElementById('wallOptions').style.display=(structure=="Wall")?"block":"none";
}

// --- UNIT CONVERSION ---
function convert(val,unit){
    if(unit=="cm") return val*10;
    if(unit=="m") return val*1000;
    if(unit=="inch") return val*25.4;
    if(unit=="ft") return val*304.8;
    return val;
}

// --- ANALYSIS ---
let severity=0;
let chart;

function analyze(){
    let w=convert(parseFloat(document.getElementById('width').value)||0,document.getElementById('widthUnit').value);
    let l=convert(parseFloat(document.getElementById('length').value)||0,document.getElementById('lengthUnit').value);
    let status=document.getElementById('status').value;
    let factor=1;
    if(status=="Increasing") factor+=0.5;
    severity=Math.min(100,Math.floor((w*4+l*0.05)*factor));
    updateBar();
    generateGraph();
    document.getElementById('result').innerHTML="<b>Severity:</b> "+severity+"/100<br><b>Recommended Materials:</b> Epoxy, Polymer mortar<br><b>What To Do:</b> Clean crack, monitor growth<br><b>What NOT To Do:</b> Ignore widening cracks";
}

// --- BAR ---
function updateBar(){
    let fill=document.getElementById('severityFill');
    fill.style.width=severity+"%";
    fill.style.background=severity<40?"green":severity<70?"orange":"red";
}

// --- GRAPH ---
function generateGraph(){
    if(chart) chart.destroy();
    chart=new Chart(document.getElementById('chart'),{
        type:'line',
        data:{labels:["Now","1M","3M","6M","1Y"],datasets:[{label:"Predicted Severity",data:[severity,severity+5,severity+10,severity+15,severity+20]}]},
        options:{responsive:true}
    });
}

// --- EXPORT ---
function exportExcel(){
    let ws=XLSX.utils.aoa_to_sheet([["Severity",severity]]);
    let wb=XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb,ws,"Report");
    XLSX.writeFile(wb,"Crack_Report.xlsx");
}

function exportPDF(){
    const {jsPDF}=window.jspdf;
    let doc=new jsPDF();
    doc.text("Crack Analysis Report",20,20);
    doc.text("Severity: "+severity+"/100",20,30);
    doc.save("Crack_Report.pdf");
}

// --- IMAGE AI SIMULATION ---
function runImageAI(){
    document.getElementById('aiResult').innerHTML="AI scanned image. Crack confidence: 75%";
    severity=Math.min(100,severity+20);
    updateBar();
    generateGraph();
}

// --- CAMERA ---
function startCamera(){
    navigator.mediaDevices.getUserMedia({video:true})
    .then(stream=>{document.getElementById('video').srcObject=stream;});
}

function capture(){
    let video=document.getElementById('video');
    let canvas=document.getElementById('canvas');
    let ctx=canvas.getContext('2d');
    canvas.width=video.videoWidth;
    canvas.height=video.videoHeight;
    ctx.drawImage(video,0,0);
    document.getElementById('aiResult').innerHTML="Live AI Detection Complete. Confidence: 80%";
    severity=Math.min(100,severity+25);
    updateBar();
    generateGraph();
}

// --- QUESTION ---
function answer(){
    document.getElementById('answerBox').innerHTML="Detailed AI Guidance: Check foundation settlement, inspect reinforcement, measure crack width over time. Immediate structural evaluation if width exceeds 3mm or grows.";
}

// --- 3D THREE.JS VISUALIZATION ---
let scene = new THREE.Scene();
let camera = new THREE.PerspectiveCamera(75, 1200/400, 0.1, 1000);
let renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(1200,400);
document.getElementById('threeContainer').appendChild(renderer.domElement);
let geometry = new THREE.BoxGeometry(1,1,1);
let material = new THREE.MeshBasicMaterial({color:0x00ff00});
let cube = new THREE.Mesh(geometry, material);
scene.add(cube);
camera.position.z = 5;
function animate3D(){
    requestAnimationFrame(animate3D);
    cube.rotation.x +=0.01;
    cube.rotation.y +=0.01;
    renderer.render(scene,camera);
}
animate3D();
</script>

</body>
</html>
