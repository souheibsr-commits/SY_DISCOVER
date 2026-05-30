# SY_DISCOVER
import { useState, useEffect, useRef } from "react";

// ============================================================
// DATA & STATE
// ============================================================
const ADMIN_PASSWORD = "SY2024admin";

const INITIAL_TRIPS = [
  {
    id: 1,
    name: "شاطئ الخريف - عيد الطبيعة",
    nameF: "Plage Lkhrayrf - Fête de la Nature",
    image: "🏖️",
    gradient: "from-cyan-500 to-blue-600",
    date: "2025-06-15",
    departure: "سكيكدة - أمام محطة القطار",
    departureF: "Skikda - Devant la gare",
    price: 1200,
    seats: 15,
    totalSeats: 40,
    program: ["07:00 تجمع وانطلاق", "09:30 وصول والاستقرار", "10:00 سباحة وأنشطة", "13:00 وجبة الغداء", "16:00 جولة استكشافية", "19:00 العودة"],
    category: "بحري",
    open: true,
    registrations: [],
  },
  {
    id: 2,
    name: "جبال الأوراس - المغامرة الكبرى",
    nameF: "Monts Aurès - La Grande Aventure",
    image: "⛰️",
    gradient: "from-emerald-500 to-teal-700",
    date: "2025-06-22",
    departure: "سكيكدة - ساحة الشهداء",
    departureF: "Skikda - Place des Martyrs",
    price: 2500,
    seats: 8,
    totalSeats: 30,
    program: ["06:00 الانطلاق", "10:00 وصول قمة الجبل", "11:00 تخييم ومشي", "13:00 غداء في الطبيعة", "15:00 استكشاف الكهوف", "20:00 العودة"],
    category: "جبلي",
    open: true,
    registrations: [],
  },
  {
    id: 3,
    name: "واحة صحراوية - ليالي النجوم",
    nameF: "Oasis Saharienne - Nuits Étoilées",
    image: "🌙",
    gradient: "from-amber-500 to-orange-700",
    date: "2025-07-05",
    departure: "باتنة - المحطة الرئيسية",
    departureF: "Batna - Gare Centrale",
    price: 4500,
    seats: 20,
    totalSeats: 25,
    program: ["08:00 انطلاق من باتنة", "14:00 وصول الواحة", "16:00 ركوب الجمال", "19:00 غروب الشمس", "21:00 عشاء صحراوي", "23:00 مراقبة النجوم"],
    category: "صحراوي",
    open: true,
    registrations: [],
  },
];

const ACTIVITIES = [
  { icon: "🌊", name: "رحلات بحرية", nameF: "Excursions Marines", desc: "استكشف الشواطئ الخلابة والمياه الصافية على طول الساحل الجزائري", color: "from-cyan-400 to-blue-500" },
  { icon: "⛰️", name: "رحلات جبلية", nameF: "Randonnées Montagneuses", desc: "تسلق القمم الشامخة واكتشف جمال الطبيعة الجزائرية البكر", color: "from-emerald-400 to-teal-600" },
  { icon: "🏕️", name: "تخييم وبقاء", nameF: "Camping & Bivouac", desc: "ليالي تحت النجوم في أجمل المواقع الطبيعية بالجزائر", color: "from-violet-400 to-purple-600" },
  { icon: "🏊", name: "سباحة وغطس", nameF: "Natation & Plongée", desc: "اكتشف العالم تحت الماء مع مدربين محترفين ومعدات متطورة", color: "from-sky-400 to-indigo-500" },
  { icon: "🧭", name: "جولات استكشافية", nameF: "Tours Exploratoires", desc: "رحلات مدروسة لاستكشاف الأماكن الأثرية والتاريخية الجزائرية", color: "from-amber-400 to-orange-600" },
  { icon: "🎯", name: "أنشطة رياضية", nameF: "Activités Sportives", desc: "كرة قدم على الشاطئ، طائرة، سواقة ATV، وأنشطة ترفيهية متنوعة", color: "from-rose-400 to-pink-600" },
];

const TEAM = [
  { name: "سيف الدين", role: "المدير العام", roleF: "Directeur Général", emoji: "👨‍💼" },
  { name: "ياسمين", role: "مسؤولة التنظيم", roleF: "Responsable Organisation", emoji: "👩‍💼" },
  { name: "عمر", role: "مرشد سياحي", roleF: "Guide Touristique", emoji: "🧭" },
  { name: "سارة", role: "مسؤولة التواصل", roleF: "Responsable Communication", emoji: "📱" },
];

// ============================================================
// HELPERS
// ============================================================
function CountdownTimer({ targetDate }) {
  const [time, setTime] = useState({ d: 0, h: 0, m: 0, s: 0 });
  useEffect(() => {
    const tick = () => {
      const diff = new Date(targetDate) - new Date();
      if (diff <= 0) return;
      setTime({
        d: Math.floor(diff / 86400000),
        h: Math.floor((diff % 86400000) / 3600000),
        m: Math.floor((diff % 3600000) / 60000),
        s: Math.floor((diff % 60000) / 1000),
      });
    };
    tick();
    const id = setInterval(tick, 1000);
    return () => clearInterval(id);
  }, [targetDate]);
  return (
    <div className="flex gap-2 justify-center my-3">
      {[["d", "يوم"], ["h", "ساعة"], ["m", "دقيقة"], ["s", "ثانية"]].map(([k, l]) => (
        <div key={k} className="flex flex-col items-center bg-white/10 backdrop-blur rounded-xl px-3 py-2 min-w-[52px]">
          <span className="text-2xl font-black text-amber-300 tabular-nums">{String(time[k]).padStart(2, "0")}</span>
          <span className="text-[9px] text-white/60 mt-0.5">{l}</span>
        </div>
      ))}
    </div>
  );
}

function SeatBar({ seats, total }) {
  const pct = Math.round(((total - seats) / total) * 100);
  return (
    <div className="mt-2">
      <div className="flex justify-between text-xs mb-1">
        <span className="text-gray-500">{total - seats} مسجل</span>
        <span className={seats === 0 ? "text-red-500 font-bold" : "text-emerald-600 font-bold"}>{seats} مقعد متبقي</span>
      </div>
      <div className="h-2 bg-gray-200 rounded-full overflow-hidden">
        <div className={`h-full rounded-full transition-all duration-700 ${pct > 80 ? "bg-red-500" : pct > 50 ? "bg-amber-500" : "bg-emerald-500"}`} style={{ width: `${pct}%` }} />
      </div>
    </div>
  );
}

// ============================================================
// MAIN APP
// ============================================================
export default function SyDiscover() {
  const [lang, setLang] = useState("ar");
  const [page, setPage] = useState("home");
  const [trips, setTrips] = useState(INITIAL_TRIPS);
  const [selectedTrip, setSelectedTrip] = useState(null);
  const [showRegForm, setShowRegForm] = useState(false);
  const [regSuccess, setRegSuccess] = useState(false);
  const [regData, setRegData] = useState({ name: "", phone: "", email: "", age: "", companions: 0, departure: "", notes: "" });
  const [adminLoggedIn, setAdminLoggedIn] = useState(false);
  const [adminPass, setAdminPass] = useState("");
  const [adminError, setAdminError] = useState("");
  const [adminTab, setAdminTab] = useState("trips");
  const [editTrip, setEditTrip] = useState(null);
  const [newTrip, setNewTrip] = useState({ name: "", date: "", departure: "", price: 1200, seats: 30, totalSeats: 30, program: "", category: "بحري", gradient: "from-cyan-500 to-blue-600", open: true });
  const [showNewTrip, setShowNewTrip] = useState(false);
  const [notification, setNotification] = useState(null);
  const [mobileMenu, setMobileMenu] = useState(false);
  const [contactForm, setContactForm] = useState({ name: "", email: "", phone: "", msg: "" });
  const [contactSent, setContactSent] = useState(false);
  const [msgText, setMsgText] = useState("");
  const [msgSent, setMsgSent] = useState(false);
  const ar = lang === "ar";

  const notify = (msg, type = "success") => {
    setNotification({ msg, type });
    setTimeout(() => setNotification(null), 3500);
  };

  const navLinks = [
    { id: "home", label: ar ? "الرئيسية" : "Accueil" },
    { id: "trips", label: ar ? "الرحلات" : "Voyages" },
    { id: "activities", label: ar ? "الأنشطة" : "Activités" },
    { id: "about", label: ar ? "من نحن" : "À Propos" },
    { id: "contact", label: ar ? "اتصل بنا" : "Contact" },
  ];

  const handleRegSubmit = (e) => {
    e.preventDefault();
    if (!selectedTrip) return;
    const updated = trips.map((t) => {
      if (t.id === selectedTrip.id) {
        return { ...t, seats: Math.max(0, t.seats - 1), registrations: [...t.registrations, { ...regData, tripId: t.id, date: new Date().toLocaleDateString() }] };
      }
      return t;
    });
    setTrips(updated);
    setRegSuccess(true);
    setRegData({ name: "", phone: "", email: "", age: "", companions: 0, departure: "", notes: "" });
    setTimeout(() => { setRegSuccess(false); setShowRegForm(false); setSelectedTrip(null); }, 3000);
  };

  const handleDeleteTrip = (id) => {
    if (confirm("هل أنت متأكد من حذف هذه الرحلة؟")) {
      setTrips(trips.filter((t) => t.id !== id));
      notify("تم حذف الرحلة بنجاح");
    }
  };

  const handleAddTrip = (e) => {
    e.preventDefault();
    const t = { ...newTrip, id: Date.now(), image: "🗺️", nameF: newTrip.name, departureF: newTrip.departure, program: newTrip.program.split("\n").filter(Boolean), registrations: [] };
    setTrips([...trips, t]);
    setShowNewTrip(false);
    setNewTrip({ name: "", date: "", departure: "", price: 1200, seats: 30, totalSeats: 30, program: "", category: "بحري", gradient: "from-cyan-500 to-blue-600", open: true });
    notify("تمت إضافة الرحلة بنجاح ✓");
  };

  const handleToggleTrip = (id) => {
    setTrips(trips.map((t) => t.id === id ? { ...t, open: !t.open } : t));
    notify("تم تغيير حالة التسجيل");
  };

  const allRegistrations = trips.flatMap((t) => t.registrations.map((r) => ({ ...r, tripName: t.name })));

  const exportCSV = () => {
    const header = "الاسم,الهاتف,البريد,العمر,المرافقون,نقطة الانطلاق,الرحلة,التاريخ\n";
    const rows = allRegistrations.map((r) => `${r.name},${r.phone},${r.email},${r.age},${r.companions},${r.departure},${r.tripName},${r.date}`).join("\n");
    const blob = new Blob(["\uFEFF" + header + rows], { type: "text/csv;charset=utf-8" });
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a"); a.href = url; a.download = "sy_discover_registrations.csv"; a.click();
    notify("تم تصدير البيانات ✓");
  };

  // ============================================================
  // RENDER PAGES
  // ============================================================

  const renderNav = () => (
    <nav className="fixed top-0 left-0 right-0 z-50 bg-slate-900/95 backdrop-blur-md border-b border-white/10">
      <div className="max-w-6xl mx-auto px-4 h-16 flex items-center justify-between">
        <button onClick={() => setPage("home")} className="flex items-center gap-2 group">
          <div className="w-9 h-9 rounded-full bg-gradient-to-br from-amber-400 to-teal-500 flex items-center justify-center text-lg shadow-lg group-hover:scale-110 transition-transform">🧭</div>
          <span className="font-black text-white text-xl tracking-widest">SY_DISCOVER</span>
        </button>

        {/* Desktop links */}
        <div className="hidden md:flex items-center gap-1">
          {navLinks.map((l) => (
            <button key={l.id} onClick={() => { setPage(l.id); setMobileMenu(false); }}
              className={`px-4 py-2 rounded-lg text-sm font-semibold transition-all ${page === l.id ? "bg-amber-500 text-slate-900" : "text-white/70 hover:text-white hover:bg-white/10"}`}>
              {l.label}
            </button>
          ))}
          <button onClick={() => setPage("admin")} className="ml-2 px-3 py-2 rounded-lg text-xs font-bold border border-amber-500/40 text-amber-400 hover:bg-amber-500/10 transition-all">
            {ar ? "الإدارة" : "Admin"}
          </button>
          <button onClick={() => setLang(ar ? "fr" : "ar")} className="ml-1 px-3 py-1.5 rounded-full bg-white/10 text-white/80 text-xs font-bold hover:bg-white/20 transition-all">
            {ar ? "FR" : "عر"}
          </button>
        </div>

        {/* Mobile menu button */}
        <button className="md:hidden text-white text-2xl" onClick={() => setMobileMenu(!mobileMenu)}>
          {mobileMenu ? "✕" : "☰"}
        </button>
      </div>

      {mobileMenu && (
        <div className="md:hidden bg-slate-900 border-t border-white/10 px-4 py-3 flex flex-col gap-2">
          {navLinks.map((l) => (
            <button key={l.id} onClick={() => { setPage(l.id); setMobileMenu(false); }}
              className={`text-left px-4 py-2.5 rounded-lg text-sm font-semibold transition-all ${page === l.id ? "bg-amber-500 text-slate-900" : "text-white/80 hover:bg-white/10"}`}>
              {l.label}
            </button>
          ))}
          <div className="flex gap-2 mt-1">
            <button onClick={() => { setPage("admin"); setMobileMenu(false); }} className="flex-1 py-2 rounded-lg border border-amber-500/40 text-amber-400 text-sm font-bold">Admin</button>
            <button onClick={() => setLang(ar ? "fr" : "ar")} className="flex-1 py-2 rounded-lg bg-white/10 text-white text-sm font-bold">{ar ? "FR" : "عر"}</button>
          </div>
        </div>
      )}
    </nav>
  );

  const renderHome = () => (
    <div className="pt-16">
      {/* Hero */}
      <section className="relative min-h-screen flex items-center overflow-hidden bg-slate-900">
        <div className="absolute inset-0">
          <div className="absolute inset-0 bg-gradient-to-br from-slate-900 via-teal-950 to-slate-900" />
          <div className="absolute top-20 left-1/4 w-96 h-96 rounded-full bg-teal-500/10 blur-3xl animate-pulse" />
          <div className="absolute bottom-20 right-1/4 w-80 h-80 rounded-full bg-amber-500/10 blur-3xl animate-pulse" style={{animationDelay:"1.5s"}} />
          {/* Animated waves */}
          <svg className="absolute bottom-0 w-full" viewBox="0 0 1440 200" fill="none">
            <path d="M0,80 Q180,0 360,80 Q540,160 720,80 Q900,0 1080,80 Q1260,160 1440,80 L1440,200 L0,200Z" fill="rgba(20,184,166,0.08)">
              <animate attributeName="d" dur="6s" repeatCount="indefinite"
                values="M0,80 Q180,0 360,80 Q540,160 720,80 Q900,0 1080,80 Q1260,160 1440,80 L1440,200 L0,200Z;
                        M0,100 Q180,20 360,100 Q540,180 720,100 Q900,20 1080,100 Q1260,180 1440,100 L1440,200 L0,200Z;
                        M0,80 Q180,0 360,80 Q540,160 720,80 Q900,0 1080,80 Q1260,160 1440,80 L1440,200 L0,200Z"/>
            </path>
            <path d="M0,120 Q240,60 480,120 Q720,180 960,120 Q1200,60 1440,120 L1440,200 L0,200Z" fill="rgba(245,158,11,0.06)">
              <animate attributeName="d" dur="8s" repeatCount="indefinite"
                values="M0,120 Q240,60 480,120 Q720,180 960,120 Q1200,60 1440,120 L1440,200 L0,200Z;
                        M0,140 Q240,80 480,140 Q720,200 960,140 Q1200,80 1440,140 L1440,200 L0,200Z;
                        M0,120 Q240,60 480,120 Q720,180 960,120 Q1200,60 1440,120 L1440,200 L0,200Z"/>
            </path>
          </svg>
        </div>

        <div className="relative max-w-5xl mx-auto px-6 text-center z-10">
          <div className="inline-flex items-center gap-2 bg-amber-500/15 border border-amber-500/30 rounded-full px-4 py-1.5 mb-6">
            <span className="w-2 h-2 rounded-full bg-amber-400 animate-pulse" />
            <span className="text-amber-300 text-sm font-semibold">{ar ? "رحلات سياحية في الجزائر" : "Voyages Touristiques en Algérie"}</span>
          </div>

          <h1 className="text-6xl md:text-8xl font-black text-white mb-4 tracking-tight">
            SY_<span className="text-transparent bg-clip-text bg-gradient-to-r from-amber-400 via-teal-400 to-amber-400">DISCOVER</span>
          </h1>
          <p className="text-xl md:text-2xl text-white/60 mb-2 font-light">
            {ar ? "اكتشف جمال الجزائر مع فريق محترف ومنظم" : "Découvrez la beauté de l'Algérie avec une équipe professionnelle"}
          </p>
          <p className="text-white/40 mb-10 max-w-xl mx-auto">
            {ar ? "رحلات بحرية وجبلية وصحراوية مع أفضل التجارب السياحية وأعلى معايير الأمان والجودة"
              : "Excursions marines, montagneuses et sahariennes avec les meilleures expériences touristiques"}
          </p>

          <div className="flex flex-col sm:flex-row gap-4 justify-center">
            <button onClick={() => setPage("trips")}
              className="px-8 py-4 bg-gradient-to-r from-amber-500 to-amber-600 text-slate-900 rounded-2xl font-black text-lg shadow-xl hover:shadow-amber-500/30 hover:scale-105 transition-all">
              {ar ? "🗺️ استكشف الرحلات" : "🗺️ Explorer les Voyages"}
            </button>
            <button onClick={() => setPage("activities")}
              className="px-8 py-4 bg-white/10 text-white rounded-2xl font-bold text-lg border border-white/20 hover:bg-white/20 transition-all">
              {ar ? "🎯 الأنشطة" : "🎯 Activités"}
            </button>
          </div>

          {/* Stats */}
          <div className="grid grid-cols-3 gap-6 mt-16 max-w-lg mx-auto">
            {[["500+", ar ? "رحلة ناجحة" : "Voyages"], ["2000+", ar ? "عميل سعيد" : "Clients"], ["100%", ar ? "رضا مضمون" : "Satisfaction"]].map(([v, l]) => (
              <div key={l} className="text-center">
                <div className="text-3xl font-black text-amber-400">{v}</div>
                <div className="text-white/50 text-sm mt-1">{l}</div>
              </div>
            ))}
          </div>
        </div>
      </section>

      {/* Next trip countdown */}
      {trips.filter(t => t.open).length > 0 && (
        <section className="bg-gradient-to-r from-teal-600 to-teal-700 py-10 px-6 text-center">
          <p className="text-white/80 text-sm mb-1">{ar ? "الرحلة القادمة:" : "Prochain voyage:"}</p>
          <h3 className="text-white text-2xl font-black mb-1">{ar ? trips.filter(t=>t.open)[0].name : trips.filter(t=>t.open)[0].nameF}</h3>
          <CountdownTimer targetDate={trips.filter(t=>t.open)[0].date} />
          <button onClick={() => { setSelectedTrip(trips.filter(t=>t.open)[0]); setShowRegForm(true); }}
            className="mt-3 px-6 py-3 bg-amber-500 text-slate-900 rounded-xl font-black hover:scale-105 transition-transform shadow-lg">
            {ar ? "سجل الآن ←" : "S'inscrire →"}
          </button>
        </section>
      )}

      {/* Featured trips */}
      <section className="bg-slate-50 py-20 px-6">
        <div className="max-w-6xl mx-auto">
          <div className="text-center mb-12">
            <h2 className="text-4xl font-black text-slate-800 mb-3">{ar ? "الرحلات القادمة" : "Voyages à Venir"}</h2>
            <div className="w-20 h-1 bg-gradient-to-r from-amber-400 to-teal-500 mx-auto rounded-full" />
          </div>
          <div className="grid md:grid-cols-3 gap-6">
            {trips.slice(0, 3).map((t) => (
              <TripCard key={t.id} trip={t} ar={ar} onRegister={() => { setSelectedTrip(t); setShowRegForm(true); }} onView={() => setPage("trips")} />
            ))}
          </div>
          <div className="text-center mt-10">
            <button onClick={() => setPage("trips")} className="px-8 py-3 bg-slate-900 text-white rounded-xl font-bold hover:bg-slate-700 transition-all">
              {ar ? "عرض جميع الرحلات →" : "Voir tous les voyages →"}
            </button>
          </div>
        </div>
      </section>

      {/* About preview */}
      <section className="bg-white py-20 px-6">
        <div className="max-w-4xl mx-auto text-center">
          <div className="text-6xl mb-6">🧭</div>
          <h2 className="text-4xl font-black text-slate-800 mb-4">{ar ? "من نحن؟" : "Qui Sommes-Nous?"}</h2>
          <p className="text-slate-600 text-lg leading-relaxed mb-6">
            {ar ? "SY_DISCOVER هي مؤسسة سياحية جزائرية متخصصة في تنظيم رحلات مميزة وفريدة. نقدم تجارب سياحية استثنائية على طول الساحل الجزائري وفي عمق الجبال والصحراء. فريقنا من المرشدين المعتمدين يضمن لك رحلة آمنة لا تُنسى."
              : "SY_DISCOVER est une agence touristique algérienne spécialisée dans l'organisation de voyages uniques et inoubliables. Nous offrons des expériences exceptionnelles le long de la côte algérienne et dans les profondeurs des montagnes et du désert."}
          </p>
          <button onClick={() => setPage("about")} className="px-6 py-3 border-2 border-teal-500 text-teal-600 rounded-xl font-bold hover:bg-teal-50 transition-all">
            {ar ? "اقرأ المزيد" : "En Savoir Plus"}
          </button>
        </div>
      </section>
    </div>
  );

  // Trip Card component
  const TripCard = ({ trip, ar, onRegister, onView }) => (
    <div className="bg-white rounded-2xl overflow-hidden shadow-md hover:shadow-xl transition-all duration-300 hover:-translate-y-1 border border-slate-100">
      <div className={`bg-gradient-to-br ${trip.gradient} h-44 flex items-center justify-center relative`}>
        <span className="text-7xl">{trip.image}</span>
        <div classNa
