# -*- coding: utf-8 -*-

html_content = """<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>سين جيم الطبي - النسخة المطورة</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'Segoe UI', Tahoma, Arial, sans-serif; background: #f5f4f0; color: #1a1a18; min-height: 100vh; }

  .screen { display: none; padding: 2rem 1.5rem; max-width: 950px; margin: 0 auto; }
  .screen.active { display: block; }

  h1 { font-size: 26px; font-weight: 600; text-align: center; margin-bottom: 0.5rem; color: #1a1a18; }
  .subtitle { font-size: 15px; color: #6b6b68; text-align: center; margin-bottom: 2rem; }
  .logo { text-align: center; padding: 2rem 0 1rem; font-size: 52px; }

  label { font-size: 14px; color: #6b6b68; display: block; margin-bottom: 6px; margin-top: 1rem; }
  input[type=text], input[type=password] {
    width: 100%; padding: 10px 14px; border: 1px solid #d3d1c7; border-radius: 8px;
    font-size: 15px; font-family: inherit; background: #fff; color: #1a1a18;
    outline: none; direction: rtl;
  }
  input:focus { border-color: #378add; box-shadow: 0 0 0 3px rgba(55,138,221,0.15); }
  .error { font-size: 13px; color: #c0392b; margin-top: 4px; display: none; }

  .btn {
    width: 100%; padding: 11px; border: 1px solid #b4b2a9; border-radius: 8px;
    background: transparent; color: #1a1a18; font-size: 15px; font-family: inherit;
    cursor: pointer; margin-top: 1rem; transition: background 0.15s;
  }
  .btn:hover { background: #f1efe8; }
  .btn.primary { background: #378add; color: #fff; border-color: #378add; }
  .btn.primary:hover { background: #185fa5; }
  .btn.success { border-color: #3b6d11; color: #3b6d11; }
  .btn.success:hover { background: #eaf3de; }
  .btn.danger { border-color: #a32d2d; color: #a32d2d; }
  .btn.danger:hover { background: #fcebeb; }
  .btn.muted { border-color: #b4b2a9; color: #6b6b68; }

  /* Scores */
  .scores { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 1rem; }
  .score-card {
    background: #fff; border-radius: 12px; padding: 1rem; text-align: center;
    border: 2px solid transparent; transition: border-color 0.2s;
    box-shadow: 0 1px 4px rgba(0,0,0,0.06);
  }
  .score-card.active-turn { border-color: #378add; }
  .score-card .team-name { font-size: 13px; color: #6b6b68; margin-bottom: 4px; }
  .score-card .team-score { font-size: 30px; font-weight: 600; color: #1a1a18; }
  .score-card .turn-badge {
    font-size: 11px; background: #e6f1fb; color: #185fa5;
    padding: 2px 10px; border-radius: 6px; display: inline-block; margin-top: 4px;
  }

  .turn-indicator { font-size: 14px; color: #6b6b68; text-align: center; margin-bottom: 1rem; }
  .turn-indicator span { color: #185fa5; font-weight: 600; }

  /* Board */
  .board-wrap { overflow-x: auto; }
  table { width: 100%; border-collapse: separate; border-spacing: 5px; }
  th {
    background: #fff; border: 1px solid #d3d1c7; border-radius: 8px;
    padding: 10px 4px; font-size: 12px; font-weight: 600; text-align: center;
    color: #1a1a18; min-width: 110px;
  }
  .cell {
    background: #e6f1fb; border: 1px solid #b5d4f4; border-radius: 8px;
    padding: 14px 4px; text-align: center; font-size: 15px; font-weight: 600;
    color: #185fa5; cursor: pointer; transition: background 0.15s; min-width: 80px;
  }
  .cell:hover { background: #378add; color: #fff; }
  .cell.used {
    background: #f1efe8; color: #b4b2a9; cursor: default;
    border-color: #d3d1c7; pointer-events: none;
  }

  /* Modal */
  .modal-bg {
    position: fixed; top:0; left:0; right:0; bottom:0;
    background: rgba(0,0,0,0.55); display:flex;
    align-items:center; justify-content:center; z-index:100;
  }
  .modal {
    background: #fff; border-radius: 16px; padding: 1.75rem;
    max-width: 500px; width: 92%; border: 1px solid #d3d1c7;
    box-shadow: 0 8px 32px rgba(0,0,0,0.18);
  }
  .modal-cat { font-size: 13px; color: #6b6b68; margin-bottom: 6px; }
  .pts-badge {
    font-size: 13px; background: #e6f1fb; color: #185fa5;
    padding: 4px 12px; border-radius: 6px; display: inline-block; margin-bottom: 1rem;
  }
  .question-text { font-size: 18px; font-weight: 500; color: #1a1a18; margin-bottom: 1.5rem; line-height: 1.6; }
  .answer-box {
    background: #eaf3de; border-radius: 8px; padding: 1rem;
    margin-bottom: 1rem; display: none; border: 1px solid #c0dd97;
  }
  .answer-label { font-size: 12px; color: #3b6d11; margin-bottom: 4px; font-weight: 600; }
  .answer-text { font-size: 15px; color: #1a1a18; }
  .modal-btns { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 8px; }
  .btn-full { grid-column: 1 / -1; }

  /* End Screen */
  .winner-box {
    background: #eaf3de; border-radius: 12px; padding: 1.5rem;
    text-align: center; margin: 1.5rem 0; border: 1px solid #c0dd97;
  }
  .winner-label { font-size: 14px; color: #3b6d11; margin-bottom: 6px; }
  .winner-name { font-size: 26px; font-weight: 600; color: #27500a; }
  .draw-text { font-size: 20px; font-weight: 600; color: #6b6b68; }
  .final-scores { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 1.5rem; }
</style>
</head>
<body>

<!-- Login -->
<div id="screen-login" class="screen active">
  <div class="logo">🚑</div>
  <h1>سين جيم الطبي</h1>
  <p class="subtitle">لعبة المسعفين الاحترافية - 6 فئات وأسئلة متجددة</p>
  <label>اسم المستخدم</label>
  <input id="login-user" type="text" placeholder="أدخل اسمك" />
  <div class="error" id="err-login">الرجاء إدخال اسم المستخدم.</div>
  <label>كلمة المرور</label>
  <input id="login-pass" type="password" placeholder="••••••" />
  <div class="error" id="err-pass">كلمة المرور غلط.</div>
  <button class="btn primary" onclick="doLogin()">دخول</button>
</div>

<!-- Teams -->
<div id="screen-teams" class="screen">
  <h1>تسمية الفرق</h1>
  <p class="subtitle">أدخل اسم كل فريق قبل بداية اللعبة</p>
  <label>اسم الفريق الأول</label>
  <input id="team1-name" type="text" placeholder="مثال: الفرقة الأولى" />
  <div class="error" id="err-team1">الرجاء إدخال اسم الفريق الأول.</div>
  <label>اسم الفريق الثاني</label>
  <input id="team2-name" type="text" placeholder="مثال: الفرقة الثانية" />
  <div class="error" id="err-team2">الرجاء إدخال اسم الفريق الثاني.</div>
  <button class="btn primary" onclick="startGame()">ابدأ اللعبة</button>
</div>

<!-- Board -->
<div id="screen-board" class="screen">
  <div class="scores" id="scores-display"></div>
  <div class="turn-indicator">دور فريق: <span id="current-turn-name">—</span></div>
  <div class="board-wrap">
    <table id="game-table"></table>
  </div>
</div>

<!-- Modal -->
<div id="modal" class="modal-bg" style="display:none" onclick="bgClose(event)">
  <div class="modal" dir="rtl">
    <div class="modal-cat" id="modal-cat"></div>
    <div class="pts-badge" id="modal-pts"></div>
    <div class="question-text" id="modal-q"></div>
    <div class="answer-box" id="modal-ans">
      <div class="answer-label">✅ الإجابة الصحيحة:</div>
      <div class="answer-text" id="modal-ans-text"></div>
    </div>
    <div class="modal-btns">
      <button class="btn primary btn-full" id="btn-show" onclick="showAnswer()">اظهر الإجابة</button>
      <button class="btn success" id="btn-correct" style="display:none" onclick="givePoints(true)">✓ صح</button>
      <button class="btn danger" id="btn-wrong" style="display:none" onclick="givePoints(false)">✗ خطأ</button>
      <button class="btn muted" id="btn-skip" style="display:none" onclick="skipQ()">تجاوز</button>
    </div>
  </div>
</div>

<!-- End -->
<div id="screen-end" class="screen">
  <h1>🏆 انتهت اللعبة!</h1>
  <div class="winner-box" id="winner-box"></div>
  <div class="final-scores" id="final-scores"></div>
  <button class="btn primary" onclick="restartGame()">العب مرة ثانية</button>
  <button class="btn" onclick="newTeams()" style="margin-top:8px">تغيير الفرق</button>
</div>

<script>
const PASSWORD = "ems2024";

// الفئات الستة المطلوبة بالكامل
const categories = [
  "التشريح", 
  "الإحالة", 
  "الأدوات والمعدات", 
  "العلامات الحيوية", 
  "الأدوية", 
  "الطوارئ والإسعافات"
];

// بنك الأسئلة الضخم (كل فئة تحتوي على أسئلة متنوعة لاختيار عشوائي دون تكرار)
const questionBank = {
  "التشريح": [
    { q: "ما هو العضو المسؤول عن ضخ الدم في الجسم؟", a: "القلب" },
    { q: "كم عدد عظام الجسم البشري البالغ؟", a: "206 عظمة" },
    { q: "ما هو اسم الحجاب الفاصل بين تجويف الصدر والبطن؟", a: "الحجاب الحاجز (Diaphragm)" },
    { q: "ما هو الوريد الأكبر الذي يصب في الأذين الأيمن للقلب؟", a: "الوريد الأجوف (Vena Cava)" },
    { q: "أين يقع الطحال في جسم الإنسان؟", a: "الجانب الأيسر من البطن تحت القفص الصدري" },
    { q: "ما هو الشريان الرئيسي الذي يخرج من البطين الأيسر؟", a: "الشريان الأورطي (Aorta)" },
    { q: "كم عدد فقرات العمود الفقري العنقي؟", a: "7 فقرات" },
    { q: "ما هو العضو المسؤول عن تنقية الدم وإצהار السموم؟", a: "الكبد" },
    { q: "أين تقع الغدة الكظرية (الجار كلوية)؟", a: "فوق الكليتين" },
    { q: "ما هي الصمامات الموجودة بين الأذينين والبطينين؟", a: "الصمام المترالي وثلاثي الشرفات" }
  ],
  "الإحالة": [
    { q: "مريض عمره 45 سنة يشكو من ألم في الصدر ينتشر للكتف الأيسر مع تعرق. ما الإحالة؟", a: "إحالة طارئة إلى طوارئ القلب مع إنذار مسبق" },
    { q: "طفل 3 سنوات يعاني من صعوبة في التنفس وصوت صفير حاد. ما الإحالة المناسبة؟", a: "إحالة إلى طوارئ الأطفال بشكل عاجل" },
    { q: "مريضة حامل تشتكي من نزيف مهبلي مع ألم بطني شديد. ما الإجراء؟", a: "إحالة طارئة إلى مستشفى ولادة مع مراقبة العلامات" },
    { q: "مريض سقط من ارتفاع 3 أمتار ويشكو من ألم في الرقبة. ما الإجراء الأولي؟", a: "تثبيت العمود الفقري وإحالة لطوارئ الرضوض" },
    { q: "مريض يعاني من جرح عميق في الفخذ مع نزيف شرياني. ما الإحالة؟", a: "رباط ضاغط وإحالة فورية لطوارئ الجراحة" },
    { q: "شخص ظهرت عليه أعراض وجه مائل وضعف في جانب واحد وصعوبة كلام. ما الإحالة؟", a: "كود جلطة دماغية (Stroke) وإحالة فورية لمركز السكتات" },
    { q: "مصاب بحروق تغطي الصدر والوجه من درجة ثانية. ما الإحالة؟", a: "إحالة طارئة لمركز الحروق المتخصص" },
    { q: "مريض سكري واعٍ لكنه يعاني من تشوش واضطراب تركيز شديد. ما الإحالة؟", a: "قياس السكر وإحالة للطوارئ لتقييم الهبوط/الارتفاع" },
    { q: "مصاب بحادث تصادم سيارة ويعاني من ألم بطني حاد وكدمات حزام الأمان. ما الإحالة؟", a: "إحالة طارئة للاشتباه بنزيف داخلي" },
    { q: "مسن يعاني من ضيق تنفس متزايد مع تورم في القدمين. ما الإحالة؟", a: "إحالة لطوارئ الباطنية لفشل القلب الاحتقاني" }
  ],
  "الأدوات والمعدات": [
    { q: "ما هي أداة قياس نسبة الأكسجين في الدم؟", a: "مقياس النبض والأكسجين (Pulse Oximeter)" },
    { q: "ما هو اسم الجهاز الذي يستخدم لصعقات القلب الكهربائية؟", a: "جهاز إزالة الرجفان (Defibrillator / AED)" },
    { q: "ما هي الأداة المستخدمة لتثبيت العمود الفقري العنقي؟", a: "طوق عنق الرقبة (Cervical Collar)" },
    { q: "ما هو جهاز BVM المستخدم في الإسعاف؟", a: "Bag Valve Mask - كيس التنفس الصناعي" },
    { q: "ما الفرق بين جبيرة SAM Splint وجبيرة الهواء؟", a: "SAM مرنة وتُشكَّل يدوياً، والهواء قابلة للنفخ" },
    { q: "ما هي الوظيفة الأساسية لجهاز شفط السويدي (Suction Unit)؟", a: "إزالة الإفرازات والدم من مجرى الهواء" },
    { q: "ما هو اسم اللوح الخشبي المستخدم لنقل مصابات الحوادث والعمود الفقري؟", a: "لوح التثبيت الطويل (Spine Board)" },
    { q: "متى يُستخدم قناع الوجه غير المتنفس (Non-Rebreather Mask)؟", a: "لإعطاء تركيز عالي من الأكسجين (حتى 95%)" },
    { q: "ما هي أداة قياس ضغط الدم اليدوية؟", a: "جهاز قياس الضغط الزئبقي أو الهوائي (Sphygmomanometer)" },
    { q: "ما هو استخدام أنبوب البلعوم الفموي (OPA)؟", a: "منع لسان المريض فاقد الوعي من إغلاق مجرى الهواء" }
  ],
  "العلامات الحيوية": [
    { q: "ما معنى مقياس GCS وما الدرجة الطبيعية؟", a: "Glasgow Coma Scale لقياس الوعي — الطبيعية 15" },
    { q: "كم معدل الأكسجين الطبيعي في دم الإنسان؟", a: "95% إلى 100%" },
    { q: "ما هو معدل التنفس الطبيعي عند البالغ السليم؟", a: "12 إلى 20 نفساً في الدقيقة" },
    { q: "ما هو معدل نبض القلب الطبيعي للبالغ أثناء الراحة؟", a: "60 إلى 100 نبضة في الدقيقة" },
    { q: "ما هي قراءة ضغط الدم الطبيعية المثالية تقريباً؟", a: "120/80 ملم زئبق" },
    { q: "ما هي درجة الحرارة الطبيعية للجسم عبر الفم؟", a: "حوالي 36.5 إلى 37.5 درجة مئوية" },
    { q: "ماذا تعني علامة شحوب الجلد وبرودته للمسعف؟", a: "احتمال وجود صدمة (Shock) أو ضعف تروية" },
    { q: "كيف يُفحص تفاعل بؤبؤ العين (Pupils)؟", a: "باستخدام الضوء لتقييم الاستجابة والانكماش" },
    { q: "ما هو مؤشر ألم الصدر الذي يسأله المسعف للمريض (OPQRST)؟", a: "تقييم خصائص ومواصفات الألم بدقة" },
    { q: "متى يُعتبر ضغط الدم في حالة ارتفاع خطير (Hypertensive Crisis)؟", a: "إذا تجاوز 180/120 ملم زئبق" }
  ],
  "الأدوية": [
    { q: "ما هو الدواء الإسعافي الأول المستخدم لأزمات الربو الحادة؟", a: "بخاخ سالبيوتامول (Ventolin / Albuterol)" },
    { q: "ما هو استخدام حقنة الأبينفرين (Epinephrine / Adrenaline) الطارئة؟", a: "علاج الحساسية المفرطة والصدمة التحسسية (Anaphylaxis)" },
    { q: "متى يُعطى الأسبرين (Aspirin) لمريض الإسعاف؟", a: "للاشتباه باحتشاء عضلة القلب الحاد (ألم الصدر)" },
    { q: "ما هو استخدام دواء النيتروجليسرين (Nitroglycerin)؟", a: "توسيع الشرايين التاجية لعلاج الذبحة الصدرية" },
    { q: "ما هو الدواء المعاكس (المضاد) لتسمم المخدرات والمورفين (Naloxone)؟", a: "نالوكسون (Narcan) لعلاج تثبيط التنفس" },
    { q: "لماذا يُعطى الجلوكوز الفموي أو الوريدي؟", a: "لعلاج انخفاض السكر الشديد في الدم (Hypoglycemia)" },
    { q: "ما هو المحظور الرئيسي عند إعطاء النيتروجليسرين؟", a: "انخفاض الضغط الشديد أو أخذ أدوية ضعف الإنتصاب حديثاً" },
    { q: "كيف يُعطى الأكسجين الطبي في الحالات الطارئة كدواء؟", a: "عبر أقنعة مخصصة وبناءً على قراءة مقياس الأكسجين" },
    { q: "ما هو استخدام دواء ديفينهيدرامين (بنادريل / مضاد الهيستامين) الإسعافي؟", a: "للحساسية الجلدية وتفاعلات التحسس البسيطة" },
    { q: "لماذا يُمنع إعطاء الماء للفم لمريض فاقد الوعي أو بغيبوبة سكر؟", a: "لتجنب دخول السوائل إلى الرئتين والاختناق (Aspiration)" }
  ],
  "الطوارئ والإسعافات": [
    { q: "ما هي القاعدة الذهبية الأولى عند التعامل مع أي موقع حادث؟", a: "تأمين سلامة المسعف أولاً ثم المكان والمصاب" },
    { q: "ما هو وضع الاسترداد (Recovery Position) ومتى يستخدم؟", a: "وضع المريض على جنبه لتفادي الاختناق (لفاقدي الوعي المتنفسين)" },
    { q: "كم نسبة ضغط الصدر إلى التنفس في إنعاش القلب (CPR) للبالغين؟", a: "30 ضغطة مقابل تنفسين (30:2)" },
    { q: "ما هو الإجراء الفوري عند تعرض شخص لاختناق كامل بقطعة طعام؟", a: "مناورة هيمليك (Heimlich Maneuver) للضغط البطني" },
    { q: "كيف تتعامل مع نزيف الأنف (راعاف) الإسعافي؟", a: "إرجاع الرأس قليلاً للأمام والضغط على الأنف من الخارج" },
    { q: "ما هو الإجراء الصحيح عند الإصابة بحروق كيميائية بالجلد؟", a: "غسل الموقع بغزارة بالماء الجاري لمدة 20 دقيقة على الأقل" },
    { q: "ما هي خطوات التعامل مع مريض يعاني من نوبة صرع (Seizure)؟", a: "إبعاد الأجسام الصلبة، حماية الرأس، وعدم وضع شيء بالفم" },
    { q: "ما هو التصرف السليم عند تعرض شخص لضربة شمس شديدة؟", a: "نقله لمنظل بارد، ترطيب الجسم بالماء البارد، وتبريده" },
    { q: "كيف يتم التعامل مع الإصابة بلدغة ثعبان سامة كإسعاف أولى؟", a: "تهدئة المصاب، تثبيت الطرف المصاب، وعدم محاولة مص السم" },
    { q: "ما هي الخطوة الأولى عند الاشتباه بوجود تسرب غاز سام في المكان؟", a: "إخلاء الموقع فوراً وطلب فريق الدفاع المدني المتخصص" }
  ]
};

const POINTS = [100, 100, 200, 400, 600];

let activeGameQuestions = {};
let teams = ["", ""];
let scores = [0, 0];
let currentTurn = 0;
let used = {};
let currentCell = null;

function doLogin() {
  const u = document.getElementById("login-user").value.trim();
  const p = document.getElementById("login-pass").value.trim();
  let ok = true;
  show("err-login", !u); if (!u) ok = false;
  show("err-pass", p !== PASSWORD); if (p !== PASSWORD) ok = false;
  if (ok) showScreen("screen-teams");
}

// دالة اختيار أسئلة عشوائية فريدة لكل فئة بدون تكرار
function selectRandomQuestions() {
  activeGameQuestions = {};
  categories.forEach(cat => {
    let pool = [...(questionBank[cat] || [])];
    // خلط الأسئلة عشوائياً
    for (let i = pool.length - 1; i > 0; i--) {
      let j = Math.floor(Math.random() * (i + 1));
      [pool[i], pool[j]] = [pool[j], pool[i]];
    }
    // أخذ عدد الأسئلة بعدد صفوف اللوحة (5 صفوف)
    activeGameQuestions[cat] = pool.slice(0, POINTS.length);
  });
}

function startGame() {
  const t1 = document.getElementById("team1-name").value.trim();
  const t2 = document.getElementById("team2-name").value.trim();
  let ok = true;
  show("err-team1", !t1); if (!t1) ok = false;
  show("err-team2", !t2); if (!t2) ok = false;
  if (!ok) return;

  teams = [t1, t2];
  scores = [0, 0];
  currentTurn = 0;
  used = {};
  
  selectRandomQuestions();
  buildBoard();
  updateScores();
  showScreen("screen-board");
}

function buildBoard() {
  const table = document.getElementById("game-table");
  let html = "<thead><tr>";
  categories.forEach(c => { html += `<th>${c}</th>`; });
  html += "</tr></thead><tbody>";
  
  POINTS.forEach((pts, row) => {
    html += "<tr>";
    categories.forEach(cat => {
      const key = cat + "_" + row;
      html += `<td class="cell" id="cell-${key}" onclick="openQ('${cat.replace(/'/g,"\\'")}',${row})">${pts}</td>`;
    });
    html += "</tr>";
  });
  html += "</tbody>";
  table.innerHTML = html;
}

function updateScores() {
  document.getElementById("scores-display").innerHTML = teams.map((t, i) => `
    <div class="score-card ${i === currentTurn ? 'active-turn' : ''}">
      <div class="team-name">${t}</div>
      <div class="team-score">${scores[i]}</div>
      ${i === currentTurn ? '<div class="turn-badge">دوره الآن</div>' : ''}
    </div>
  `).join("");
  document.getElementById("current-turn-name").textContent = teams[currentTurn];
}

function openQ(cat, row) {
  const key = cat + "_" + row;
  if (used[key]) return;
  currentCell = { cat, row, key };
  
  const qObj = activeGameQuestions[cat][row];
  document.getElementById("modal-cat").textContent = "📂 " + cat;
  document.getElementById("modal-pts").textContent = POINTS[row] + " نقطة";
  document.getElementById("modal-q").textContent = qObj.q;
  document.getElementById("modal-ans-text").textContent = qObj.a;
  
  document.getElementById("modal-ans").style.display = "none";
  document.getElementById("btn-show").style.display = "block";
  document.getElementById("btn-correct").style.display = "none";
  document.getElementById("btn-wrong").style.display = "none";
  document.getElementById("btn-skip").style.display = "none";
  document.getElementById("modal").style.display = "flex";
}

function showAnswer() {
  document.getElementById("modal-ans").style.display = "block";
  document.getElementById("btn-show").style.display = "none";
  document.getElementById("btn-correct").style.display = "block";
  document.getElementById("btn-wrong").style.display = "block";
  document.getElementById("btn-skip").style.display = "block";
}

function givePoints(correct) {
  if (correct) scores[currentTurn] += POINTS[currentCell.row];
  markUsed();
  nextTurn();
  closeModal();
}

function skipQ() { markUsed(); nextTurn(); closeModal(); }

function markUsed() {
  const el = document.getElementById("cell-" + currentCell.key);
  if (el) { el.classList.add("used"); el.textContent = "✓"; }
  used[currentCell.key] = true;
}

function nextTurn() {
  currentTurn = 1 - currentTurn;
  updateScores();
  if (Object.keys(used).length === categories.length * POINTS.length) showEnd();
}

function closeModal() { document.getElementById("modal").style.display = "none"; }
function bgClose(e) { if (e.target.id === "modal") closeModal(); }

function showEnd() {
  const w = scores[0] > scores[1] ? teams[0] : scores[1] > scores[0] ? teams[1] : null;
  document.getElementById("winner-box").innerHTML = w
    ? `<div class="winner-label">🏆 الفائز</div><div class="winner-name">${w}</div>`
    : `<div class="draw-text">🤝 تعادل!</div>`;
  document.getElementById("final-scores").innerHTML = teams.map((t, i) =>
    `<div class="score-card"><div class="team-name">${t}</div><div class="team-score">${scores[i]}</div></div>`
  ).join("");
  showScreen("screen-end");
}

function restartGame() {
  scores = [0, 0]; currentTurn = 0; used = {};
  selectRandomQuestions(); // اختيار أسئلة عشوائية جديدة تماماً دون تكرار للعبة الجديدة
  buildBoard(); updateScores(); showScreen("screen-board");
}

function newTeams() { showScreen("screen-teams"); }

function showScreen(id) {
  document.querySelectorAll(".screen").forEach(s => s.classList.remove("active"));
  document.getElementById(id).classList.add("active");
}

function show(id, visible) {
  document.getElementById(id).style.display = visible ? "block" : "none";
}

document.addEventListener("keydown", e => {
  if (e.key === "Enter") {
    const active = document.querySelector(".screen.active");
    const btn = active && active.querySelector(".btn.primary");
    if (btn) btn.click();
  }
});
</script>
</body>
</html>
"""

# حفظ الملف بصيغة HTML
with open("medical_game.html", "w", encoding="utf-8") as f:
    f.write(html_content)

print("تم إنشاء ملف اللعبة بنجاح باسم 'medical_game.html'!")
