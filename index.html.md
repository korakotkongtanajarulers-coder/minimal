<!DOCTYPE html>  
<html lang="th" class="h-full bg-slate-50">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>**ระบบบันทึกเวลาและค่าแรงพนักงาน** (Employee Work & Payroll Tracker)</title>  
    <!-- Tailwind CSS CDN -->  
    <script src="https://cdn.tailwindcss.com"></script>  
    <!-- FontAwesome Icons -->  
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">  
    <!-- Google Fonts: Prompt -->  
    <link href="https://fonts.googleapis.com/css2?family=Prompt:wght@300;400;500;600;700&display=swap" rel="stylesheet">  
    <script>  
        tailwind.config = {  
            theme: {  
                extend: {  
                    fontFamily: {  
                        sans: ['Prompt', 'sans-serif'],  
                    },  
                    colors: {  
                        brand: {  
                            50: '#f0f7ff',  
                            100: '#e0effe',  
                            500: '#0284c7',  
                            600: '#0284c7',  
                            700: '#0369a1',  
                            900: '#0c4a6e',  
                        }  
                    }  
                }  
            }  
        }  
    </script>  
    <style>  
        body { font-family: 'Prompt', sans-serif; }  
        ::-webkit-scrollbar { width: 6px; height: 6px; }  
        ::-webkit-scrollbar-track { background: #f1f5f9; }  
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }  
        ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }  
    </style>  
</head>  
<body class="h-full text-slate-800 bg-slate-50 antialiased flex flex-col min-h-screen">  
  
    <!-- Top Navigation Header -->  
    <header class="bg-slate-900 text-white sticky top-0 z-30 shadow-md">  
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">  
            <div class="flex items-center justify-between h-16">  
                <div class="flex items-center space-x-3">  
                    <div class="bg-brand-600 p-2 rounded-xl flex items-center justify-center text-white shadow-lg">  
                        <i class="fa-solid fa-clipboard-user text-xl"></i>  
                    </div>  
                    <div>  
                        <h1 class="font-bold text-base sm:text-lg leading-tight">**ระบบลงเวลา** & **ค่าแรงลูกน้อง**</h1>  
                        <p class="text-[11px] text-slate-400 font-light">**จัดการเช็คชื่อ**, **ไซต์งาน**, OT **และปฏิทินสรุป**</p>  
                    </div>  
                </div>  
  
                <div class="flex items-center gap-2 sm:gap-3">  
                    <button onclick="openModal('manageSitesModal')" class="hidden sm:inline-flex items-center px-3 py-1.5 text-xs font-semibold text-slate-300 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-lg transition">  
                        <i class="fa-solid fa-location-dot mr-1.5 text-brand-500"></i> **ไซต์งาน**  
                    </button>  
                    <button onclick="setAllHolidayToday()" class="inline-flex items-center px-3 py-1.5 text-xs font-semibold text-rose-300 bg-rose-950/80 hover:bg-rose-900 border border-rose-800 rounded-lg transition shadow-sm">  
                        <i class="fa-solid fa-mug-hot mr-1.5 text-rose-400"></i> **หยุดทั้งทีม**  
                    </button>  
                    <button onclick="openModal('managePositionsModal')" class="hidden sm:inline-flex items-center px-3 py-1.5 text-xs font-semibold text-slate-300 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-lg transition">  
                        <i class="fa-solid fa-tags mr-1.5 text-brand-500"></i> **ตำแหน่ง**  
                    </button>  
                    <button onclick="openModal('addEmployeeModal')" class="inline-flex items-center px-3.5 py-1.5 text-xs font-semibold text-white bg-brand-600 hover:bg-brand-700 rounded-lg transition shadow-md">  
                        <i class="fa-solid fa-user-plus mr-1.5"></i> **เพิ่มพนักงาน**  
                    </button>  
                </div>  
            </div>  
        </div>  
    </header>  
  
    <!-- Sub Header Controls & Filters -->  
    <div class="bg-white border-b border-slate-200 sticky top-16 z-20 shadow-sm">  
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3">  
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-3">  
                  
                <!-- View Mode Switcher -->  
                <div class="flex items-center bg-slate-100 p-1 rounded-xl w-full md:w-auto">  
                    <button id="btnDailyView" onclick="switchView('daily')" class="flex-1 md:flex-none px-3 py-1.5 rounded-lg text-xs font-bold transition-all shadow-sm bg-white text-slate-800">  
                        <i class="fa-solid fa-calendar-day mr-1 text-brand-600"></i> **รายวัน**  
                    </button>  
                    <button id="btnCalendarView" onclick="switchView('calendar')" class="flex-1 md:flex-none px-3 py-1.5 rounded-lg text-xs font-bold transition-all text-slate-600 hover:text-slate-900">  
                        <i class="fa-solid fa-calendar mr-1 text-brand-600"></i> **มุมมองปฏิทิน**  
                    </button>  
                    <button id="btnMonthlyView" onclick="switchView('monthly')" class="flex-1 md:flex-none px-3 py-1.5 rounded-lg text-xs font-bold transition-all text-slate-600 hover:text-slate-900">  
                        <i class="fa-solid fa-calendar-days mr-1 text-brand-600"></i> **สรุปรายเดือน**  
                    </button>  
                </div>  
  
                <!-- Filters -->  
                <div class="flex flex-wrap items-center gap-2 w-full md:w-auto">  
                    <div id="dailyControls" class="flex items-center gap-2">  
                        <label class="text-xs font-semibold text-slate-500 whitespace-nowrap">**เลือกวันที่**:</label>  
                        <input type="date" id="selectedDate" onchange="renderDashboard()" class="bg-slate-50 border border-slate-300 rounded-lg text-xs px-3 py-1.5 focus:outline-none focus:ring-2 focus:ring-brand-500 font-medium text-slate-700">  
                    </div>  
  
                    <div id="monthlyControls" class="hidden flex items-center gap-2">  
                        <label class="text-xs font-semibold text-slate-500 whitespace-nowrap">**เลือกเดือน**:</label>  
                        <input type="month" id="selectedMonth" onchange="renderDashboard()" class="bg-slate-50 border border-slate-300 rounded-lg text-xs px-3 py-1.5 focus:outline-none focus:ring-2 focus:ring-brand-500 font-medium text-slate-700">  
                    </div>  
  
                    <select id="filterSite" onchange="renderDashboard()" class="bg-slate-50 border border-slate-300 rounded-lg text-xs px-3 py-1.5 focus:outline-none focus:ring-2 focus:ring-brand-500 text-slate-700">  
                        <option value="ALL">**ไซต์งานทั้งหมด**</option>  
                    </select>  
  
                    <div class="relative flex-1 md:flex-none">  
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-2.5 text-slate-400 text-xs"></i>  
                        <input type="text" id="searchEmp" oninput="renderDashboard()" placeholder="**ค้นหาชื่อพนักงาน**..." class="w-full md:w-40 bg-slate-50 border border-slate-300 rounded-lg text-xs pl-8 pr-3 py-1.5 focus:outline-none focus:ring-2 focus:ring-brand-500">  
                    </div>  
                </div>  
  
            </div>  
        </div>  
    </div>  
  
    <!-- Main Content Container -->  
    <main class="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 space-y-6">  
  
        <!-- Stat Cards Section -->  
        <div class="grid grid-cols-2 md:grid-cols-4 gap-4" id="statCardsContainer"></div>  
  
        <!-- Main Panel -->  
        <div class="bg-white rounded-2xl shadow-sm border border-slate-200 overflow-hidden">  
            <div class="px-6 py-4 bg-slate-50/70 border-b border-slate-200 flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3">  
                <div>  
                    <h2 class="text-sm font-bold text-slate-800 flex items-center gap-2" id="tableTitle">  
                        <i class="fa-solid fa-list-check text-brand-600"></i> **ตารางบันทึกเวลาทำงานประจำวัน**  
                    </h2>  
                    <p class="text-xs text-slate-500 mt-0.5" id="tableSubTitle">**แสดงรายการพนักงานและปรับสถานะการทำงาน**</p>  
                </div>  
                <div class="flex items-center gap-2" id="actionButtonsContainer">  
                    <button onclick="exportToCSV()" class="px-3 py-1.5 text-xs font-semibold text-emerald-700 bg-emerald-50 hover:bg-emerald-100 border border-emerald-200 rounded-lg transition flex items-center gap-1.5">  
                        <i class="fa-solid fa-file-csv text-emerald-600"></i> Export CSV  
                    </button>  
                    <button onclick="window.print()" class="px-3 py-1.5 text-xs font-semibold text-slate-700 bg-white hover:bg-slate-100 border border-slate-300 rounded-lg transition flex items-center gap-1.5">  
                        <i class="fa-solid fa-print text-slate-500"></i> **พิมพ์**  
                    </button>  
                </div>  
            </div>  
  
            <!-- Content Area (Table or Calendar) -->  
            <div id="mainContentArea" class="overflow-x-auto">  
                <table class="w-full text-left border-collapse text-xs">  
                    <thead class="bg-slate-100 text-slate-600 uppercase font-semibold text-[11px] tracking-wider border-b border-slate-200">  
                        <tr id="tableHeader"></tr>  
                    </thead>  
                    <tbody id="attendanceTableBody" class="divide-y divide-slate-100 text-slate-700 font-normal"></tbody>  
                </table>  
            </div>  
        </div>  
  
    </main>  
  
    <!-- Toast -->  
    <div id="toast" class="fixed bottom-5 right-5 z-50 transform translate-y-20 opacity-0 transition-all duration-300 bg-slate-900 text-white px-4 py-3 rounded-xl shadow-2xl flex items-center gap-3 text-xs border border-slate-700 pointer-events-none">  
        <i class="fa-solid fa-circle-check text-emerald-400 text-sm"></i>  
        <span id="toastMsg">**บันทึกข้อมูลเรียบร้อย**</span>  
    </div>  
  
    <!-- Modals -->  
    <div id="addEmployeeModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">  
        <div class="bg-white rounded-2xl shadow-2xl max-w-md w-full overflow-hidden">  
            <div class="px-6 py-4 bg-slate-900 text-white flex justify-between items-center">  
                <h3 class="font-bold text-sm flex items-center gap-2" id="empModalTitle">  
                    <i class="fa-solid fa-user-plus text-brand-500"></i> **เพิ่มข้อมูลพนักงานใหม่**  
                </h3>  
                <button onclick="closeModal('addEmployeeModal')" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>  
            </div>  
            <form id="empForm" onsubmit="saveEmployee(event)" class="p-6 space-y-4">  
                <input type="hidden" id="empEditId">  
                <div>  
                    <label class="block text-xs font-semibold text-slate-700 mb-1">**ชื่อ** - **นามสกุลลูกน้อง** *</label>  
                    <input type="text" id="empName" required placeholder="**นายสมชาย** **ใจดี**" class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none">  
                </div>  
                <div class="grid grid-cols-2 gap-3">  
                    <div>  
                        <label class="block text-xs font-semibold text-slate-700 mb-1">**ตำแหน่งงาน** *</label>  
                        <select id="empPositionSelect" onchange="toggleCustomPositionInput()" class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none"></select>  
                    </div>  
                    <div id="customPositionBox" class="hidden">  
                        <label class="block text-xs font-semibold text-slate-700 mb-1">**ระบุตำแหน่งงาน** *</label>  
                        <input type="text" id="empPositionCustom" placeholder="**ระบุเอง**..." class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none">  
                    </div>  
                </div>  
                <div class="grid grid-cols-2 gap-3">  
                    <div>  
                        <label class="block text-xs font-semibold text-slate-700 mb-1">**ประเภทค่าแรง** *</label>  
                        <select id="empWageType" class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none">  
                            <option value="DAILY">**รายวัน** (**บาท**/**วัน**)</option>  
                            <option value="HOURLY">**รายชั่วโมง** (**บาท**/**ชม**.)</option>  
                        </select>  
                    </div>  
                    <div>  
                        <label class="block text-xs font-semibold text-slate-700 mb-1">**อัตราค่าแรง** (**บาท**) *</label>  
                        <input type="number" id="empBaseWage" required min="0" placeholder="500" class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none">  
                    </div>  
                </div>  
                <div class="grid grid-cols-2 gap-3">  
                    <div>  
                        <label class="block text-xs font-semibold text-slate-700 mb-1">**วันที่เริ่มงาน**</label>  
                        <input type="date" id="empStartDate" class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none">  
                    </div>  
                    <div>  
                        <label class="block text-xs font-semibold text-slate-700 mb-1">**วันที่ลาออก** (**ถ้ามี**)</label>  
                        <input type="date" id="empResignDate" class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none">  
                    </div>  
                </div>  
                <div class="pt-4 flex justify-end gap-2 border-t border-slate-100">  
                    <button type="button" onclick="closeModal('addEmployeeModal')" class="px-4 py-2 text-xs font-semibold text-slate-600 bg-slate-100 hover:bg-slate-200 rounded-xl transition">**ยกเลิก**</button>  
                    <button type="submit" class="px-4 py-2 text-xs font-semibold text-white bg-brand-600 hover:bg-brand-700 rounded-xl transition shadow-md">**บันทึกข้อมูล**</button>  
                </div>  
            </form>  
        </div>  
    </div>  
  
    <!-- Manage Sites Modal -->  
    <div id="manageSitesModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">  
        <div class="bg-white rounded-2xl shadow-2xl max-w-sm w-full overflow-hidden">  
            <div class="px-5 py-4 bg-slate-900 text-white flex justify-between items-center">  
                <h3 class="font-bold text-sm"><i class="fa-solid fa-location-dot text-brand-500 mr-2"></i> **จัดการรายการไซต์งาน**</h3>  
                <button onclick="closeModal('manageSitesModal')" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>  
            </div>  
            <div class="p-5 space-y-4">  
                <div class="flex gap-2">  
                    <input type="text" id="newSiteInput" placeholder="**ชื่อไซต์งานใหม่**..." class="flex-1 bg-slate-50 border border-slate-300 rounded-xl px-3 py-1.5 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none">  
                    <button onclick="addSite()" class="px-3 py-1.5 text-xs font-semibold text-white bg-brand-600 hover:bg-brand-700 rounded-xl transition">**เพิ่ม**</button>  
                </div>  
                <div id="sitesList" class="space-y-2 max-h-48 overflow-y-auto pr-1"></div>  
            </div>  
        </div>  
    </div>  
  
    <!-- Manage Positions Modal -->  
    <div id="managePositionsModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">  
        <div class="bg-white rounded-2xl shadow-2xl max-w-sm w-full overflow-hidden">  
            <div class="px-5 py-4 bg-slate-900 text-white flex justify-between items-center">  
                <h3 class="font-bold text-sm"><i class="fa-solid fa-tags text-brand-500 mr-2"></i> **จัดการรายการตำแหน่งงาน**</h3>  
                <button onclick="closeModal('managePositionsModal')" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>  
            </div>  
            <div class="p-5 space-y-4">  
                <div class="flex gap-2">  
                    <input type="text" id="newPosInput" placeholder="**ชื่อตำแหน่งงานใหม่**..." class="flex-1 bg-slate-50 border border-slate-300 rounded-xl px-3 py-1.5 text-xs focus:ring-2 focus:ring-brand-500 focus:outline-none">  
                    <button onclick="addPosition()" class="px-3 py-1.5 text-xs font-semibold text-white bg-brand-600 hover:bg-brand-700 rounded-xl transition">**เพิ่ม**</button>  
                </div>  
                <div id="positionsList" class="space-y-2 max-h-48 overflow-y-auto pr-1"></div>  
            </div>  
        </div>  
    </div>  
  
    <!-- JavaScript Logic -->  
    <script>  
        let currentView = 'daily'; // 'daily', 'calendar', 'monthly'  
        let positions = ['**ช่างไม้**', '**ช่างปูน**', '**ช่างอิฐ**', '**ช่างเหล็ก**', '**กรรมกร**'];  
        let sites = ['**ไซต์** A (**หลัก**)', '**ไซต์** B (**อาพาธ**)', '**ไซต์** C (**โครงการ** 2)'];  
          
        let employees = [  
            { id: 'emp_1', name: '**นายสมชาย** **ดีมาก**', position: '**ช่างไม้**', wageType: 'DAILY', baseWage: 550, startDate: '2026-01-01', resignDate: '' },  
            { id: 'emp_2', name: '**นายวิชัย** **มาดี**', position: '**ช่างปูน**', wageType: 'DAILY', baseWage: 500, startDate: '2026-01-15', resignDate: '' },  
            { id: 'emp_3', name: '**นายอนันต์** **ขยันยิ่ง**', position: '**กรรมกร**', wageType: 'DAILY', baseWage: 400, startDate: '2026-02-01', resignDate: '' },  
            { id: 'emp_4', name: '**นายบุญมี** **งานดี**', position: '**ช่างเหล็ก**', wageType: 'HOURLY', baseWage: 70, startDate: '2026-01-10', resignDate: '' }  
        ];  
  
        let attendanceStore = {};  
  
        window.onload = function() {  
            const todayStr = new Date().toISOString().split('T')[0];  
            document.getElementById('selectedDate').value = todayStr;  
            document.getElementById('selectedMonth').value = todayStr.substring(0, 7);  
  
            loadData();  
            seedInitialAttendance();  
            populateSiteDropdowns();  
            renderDashboard();  
        };  
  
        function saveData() {  
            localStorage.setItem('payroll_positions', JSON.stringify(positions));  
            localStorage.setItem('payroll_sites', JSON.stringify(sites));  
            localStorage.setItem('payroll_employees', JSON.stringify(employees));  
            localStorage.setItem('payroll_attendance', JSON.stringify(attendanceStore));  
        }  
  
        function loadData() {  
            const p = localStorage.getItem('payroll_positions');  
            const s = localStorage.getItem('payroll_sites');  
            const e = localStorage.getItem('payroll_employees');  
            const a = localStorage.getItem('payroll_attendance');  
            if (p) positions = JSON.parse(p);  
            if (s) sites = JSON.parse(s);  
            if (e) employees = JSON.parse(e);  
            if (a) attendanceStore = JSON.parse(a);  
        }  
  
        function seedInitialAttendance() {  
            const todayStr = document.getElementById('selectedDate').value;  
            if (!attendanceStore[todayStr]) {  
                attendanceStore[todayStr] = {};  
                employees.forEach(emp => {  
                    attendanceStore[todayStr][emp.id] = {  
                        isPresent: true,  
                        sites: [sites[0] || '**ไซต์หลัก**'],  
                        workType: 'FULL',  
                        customHours: 8,  
                        otHours: 0,  
                        advance: 0  
                    };  
                });  
                saveData();  
            }  
        }  
  
        function switchView(mode) {  
            currentView = mode;  
            const btnDaily = document.getElementById('btnDailyView');  
            const btnCalendar = document.getElementById('btnCalendarView');  
            const btnMonthly = document.getElementById('btnMonthlyView');  
            const dailyControls = document.getElementById('dailyControls');  
            const monthlyControls = document.getElementById('monthlyControls');  
  
            // Reset classes  
            [btnDaily, btnCalendar, btnMonthly].forEach(b => b.className = "flex-1 md:flex-none px-3 py-1.5 rounded-lg text-xs font-bold transition-all text-slate-600 hover:text-slate-900");  
  
            if (mode === 'daily') {  
                btnDaily.className = "flex-1 md:flex-none px-3 py-1.5 rounded-lg text-xs font-bold transition-all shadow-sm bg-white text-slate-800";  
                dailyControls.classList.remove('hidden');  
                monthlyControls.classList.add('hidden');  
                document.getElementById('tableTitle').innerHTML = `<i class="fa-solid fa-list-check text-brand-600"></i> **ตารางบันทึกเวลาทำงานประจำวัน**`;  
                document.getElementById('tableSubTitle').innerText = '**แสดงรายการพนักงานและปรับสถานะการทำงานประจำวัน**';  
            } else if (mode === 'calendar') {  
                btnCalendar.className = "flex-1 md:flex-none px-3 py-1.5 rounded-lg text-xs font-bold transition-all shadow-sm bg-white text-slate-800";  
                monthlyControls.classList.remove('hidden');  
                dailyControls.classList.add('hidden');  
                document.getElementById('tableTitle').innerHTML = `<i class="fa-solid fa-calendar-days text-brand-600"></i> **มุมมองปฏิทินสรุปการทำงาน**`;  
                document.getElementById('tableSubTitle').innerText = '**คลิกวันที่ในปฏิทินเพื่อดูหรือแก้ไขการลงเวลาประจำวันนั้นๆ**';  
            } else {  
                btnMonthly.className = "flex-1 md:flex-none px-3 py-1.5 rounded-lg text-xs font-bold transition-all shadow-sm bg-white text-slate-800";  
                monthlyControls.classList.remove('hidden');  
                dailyControls.classList.add('hidden');  
                document.getElementById('tableTitle').innerHTML = `<i class="fa-solid fa-chart-pie text-brand-600"></i> **ตารางสรุปค่าแรงและเบิกเงินประจำเดือน**`;  
                document.getElementById('tableSubTitle').innerText = '**สรุปวันมาทำงาน**, OT, **ยอดเงินเบิกสะสม** **และยอดคงเหลือจ่ายสุทธิ**';  
            }  
            renderDashboard();  
        }  
  
        function calculatePay(emp, log) {  
            if (!log || !log.isPresent) return { regularPay: 0, otPay: 0, totalPay: 0, hoursWorked: 0, otHours: 0 };  
            let hours = 0;  
            if (emp.wageType === 'DAILY') {  
                if (log.workType === 'FULL') hours = 8;  
                else if (log.workType === 'HALF') hours = 4;  
                else hours = parseFloat(log.customHours || 0);  
            } else {  
                hours = parseFloat(log.customHours || 0);  
            }  
            const ot = parseFloat(log.otHours || 0);  
            const hourlyRate = emp.wageType === 'DAILY' ? (emp.baseWage / 8) : emp.baseWage;  
            const regularPay = emp.wageType === 'DAILY' && log.workType === 'FULL' ? emp.baseWage : (hours * hourlyRate);  
            const otPay = ot * hourlyRate;  
            return { regularPay, otPay, totalPay: regularPay + otPay, hoursWorked: hours, otHours: ot };  
        }  
  
        function renderDashboard() {  
            const search = document.getElementById('searchEmp').value.toLowerCase();  
            const selectedSite = document.getElementById('filterSite').value;  
  
            if (currentView === 'daily') {  
                renderDailyView(search, selectedSite);  
            } else if (currentView === 'calendar') {  
                renderCalendarView();  
            } else {  
                renderMonthlyView(search, selectedSite);  
            }  
        }  
  
        // Daily View  
        function renderDailyView(search, selectedSite) {  
            const selectedDate = document.getElementById('selectedDate').value;  
            if (!attendanceStore[selectedDate]) attendanceStore[selectedDate] = {};  
            const dayLogs = attendanceStore[selectedDate];  
  
            const filteredEmployees = employees.filter(emp => {  
                const matchSearch = emp.name.toLowerCase().includes(search) || emp.position.toLowerCase().includes(search);  
                const isActive = isEmployeeActiveOnDate(emp, selectedDate);  
                const log = dayLogs[emp.id];  
                const empSites = getLogSites(log);  
                const matchSite = selectedSite === 'ALL' || empSites.includes(selectedSite);  
                return matchSearch && isActive && matchSite;  
            });  
  
            document.getElementById('mainContentArea').innerHTML = `  
                <table class="w-full text-left border-collapse text-xs">  
                    <thead class="bg-slate-100 text-slate-600 uppercase font-semibold text-[11px] tracking-wider border-b border-slate-200">  
                        <tr>  
                            <th class="p-3.5">**ลูกน้อง** / **ตำแหน่ง**</th>  
                            <th class="p-3.5 text-center">**ไซต์งานที่ไป**</th>  
                            <th class="p-3.5 text-center">**สถานะ**</th>  
                            <th class="p-3.5 text-center">**ชม**. **ทำงาน**</th>  
                            <th class="p-3.5 text-center">**ชม**. OT</th>  
                            <th class="p-3.5 text-right">**เบิกเงิน** (**บาท**)</th>  
                            <th class="p-3.5 text-right">**รวมค่าแรง**</th>  
                            <th class="p-3.5 text-center">**จัดการ**</th>  
                        </tr>  
                    </thead>  
                    <tbody id="attendanceTableBody" class="divide-y divide-slate-100 text-slate-700 font-normal"></tbody>  
                </table>  
            `;  
  
            let totalPresent = 0, totalOT = 0, totalDailyPay = 0, totalDailyAdvance = 0;  
            const tbody = document.getElementById('attendanceTableBody');  
  
            if (filteredEmployees.length === 0) {  
                tbody.innerHTML = `<tr><td colspan="8" class="p-8 text-center text-slate-400">**ไม่พบข้อมูลพนักงานในวันที่เลือก**</td></tr>`;  
            }  
  
            filteredEmployees.forEach(emp => {  
                const log = dayLogs[emp.id] || { isPresent: false, sites: [sites[0] || ''], workType: 'FULL', customHours: 8, otHours: 0, advance: 0 };  
                const pay = calculatePay(emp, log);  
                const empSites = getLogSites(log);  
  
                if (log.isPresent) {  
                    totalPresent++;  
                    totalOT += pay.otHours;  
                    totalDailyPay += pay.totalPay;  
                }  
                totalDailyAdvance += parseFloat(log.advance || 0);  
  
                let siteBadgesHtml = sites.map(s => {  
                    const isSelected = empSites.includes(s);  
                    return `<button type="button" onclick="toggleEmployeeSite('${emp.id}', '${s}')" class="px-2 py-0.5 rounded-md text-[10px] font-medium border transition ${isSelected ? 'bg-brand-600 text-white border-brand-600 shadow-sm' : 'bg-slate-100 text-slate-600 border-slate-200 hover:bg-slate-200'}">${s} ${isSelected ? '<i class="fa-solid fa-check text-[9px] ml-0.5"></i>' : ''}</button>`;  
                }).join('');  
  
                const tr = document.createElement('tr');  
                tr.className = `hover:bg-slate-50 transition border-b border-slate-100 ${!log.isPresent ? 'bg-slate-50/50' : ''}`;  
                tr.innerHTML = `  
                    <td class="p-3.5"><div class="font-bold text-slate-800 text-xs">${emp.name}</div><div class="flex items-center gap-1.5 mt-0.5"><span class="inline-block text-[10px] bg-slate-100 text-slate-600 px-2 py-0.5 rounded">${emp.position}</span><span class="text-[10px] text-slate-400 font-mono">฿${emp.baseWage}/${emp.wageType === 'DAILY' ? '**วัน**' : '**ชม**.'}</span></div></td>  
                    <td class="p-3.5 text-center"><div class="flex flex-wrap items-center justify-center gap-1 max-w-xs mx-auto ${!log.isPresent ? 'pointer-events-none opacity-40' : ''}">${siteBadgesHtml}</div></td>  
                    <td class="p-3.5 text-center"><label class="inline-flex items-center cursor-pointer"><input type="checkbox" ${log.isPresent ? 'checked' : ''} onchange="updateAttendance('${emp.id}', 'isPresent', this.checked)" class="sr-only peer"><div class="w-11 h-6 bg-slate-200 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-slate-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-emerald-500 relative"></div><span class="ml-2 text-xs font-semibold ${log.isPresent ? 'text-emerald-600' : 'text-slate-400'}">${log.isPresent ? '**มา**' : '**ลา**/**หยุด**'}</span></label></td>  
                    <td class="p-3.5 text-center"><div class="flex items-center justify-center gap-1.5 ${!log.isPresent ? 'opacity-40 pointer-events-none' : ''}"><select onchange="updateAttendance('${emp.id}', 'workType', this.value)" class="bg-slate-50 border border-slate-300 rounded-lg text-xs px-2 py-1"><option value="FULL" ${log.workType === 'FULL' ? 'selected' : ''}>**เต็มวัน**</option><option value="HALF" ${log.workType === 'HALF' ? 'selected' : ''}>**ครึ่งวัน**</option><option value="CUSTOM" ${log.workType === 'CUSTOM' ? 'selected' : ''}>**ระบุ** **ชม**.</option></select>${log.workType === 'CUSTOM' ? `<input type="number" min="1" max="24" value="${log.customHours || 8}" onchange="updateAttendance('${emp.id}', 'customHours', this.value)" class="w-14 bg-white border border-slate-300 rounded-lg text-xs px-2 py-1 text-center font-bold">` : ''}</div></td>  
                    <td class="p-3.5 text-center"><div class="flex items-center justify-center gap-1 ${!log.isPresent ? 'opacity-40 pointer-events-none' : ''}"><input type="number" min="0" max="16" value="${log.otHours || 0}" onchange="updateAttendance('${emp.id}', 'otHours', this.value)" class="w-16 bg-amber-50 border border-amber-300 text-amber-900 font-bold rounded-lg text-xs px-2 py-1 text-center"><span class="text-[10px] text-slate-400">**ชม**.</span></div></td>  
                    <td class="p-3.5 text-right font-mono"><input type="number" min="0" value="${log.advance || 0}" onchange="updateAttendance('${emp.id}', 'advance', this.value)" class="w-20 bg-rose-50 border border-rose-200 text-rose-800 font-bold rounded-lg text-xs px-2 py-1 text-right"></td>  
                    <td class="p-3.5 text-right font-bold text-slate-800 font-mono text-sm">฿${log.isPresent ? pay.totalPay.toLocaleString() : 0}</td>  
                    <td class="p-3.5 text-center"><button onclick="editEmployee('${emp.id}')" class="text-slate-400 hover:text-brand-600 transition p-1"><i class="fa-solid fa-pen"></i></button><button onclick="deleteEmployee('${emp.id}')" class="text-slate-400 hover:text-rose-600 transition p-1 ml-1"><i class="fa-solid fa-trash"></i></button></td>  
                `;  
                tbody.appendChild(tr);  
            });  
  
            renderStatCards([  
                { title: '**มาทำงานวันนี้**', value: `${totalPresent} / ${filteredEmployees.length} **คน**`, sub: `**ลา**/**หยุด**: ${filteredEmployees.length - totalPresent} **คน**`, icon: 'fa-user-check', color: 'emerald' },  
                { title: '**รวมชั่วโมง** OT **วันนี้**', value: `${totalOT} **ชั่วโมง**`, sub: `**อัตราเท่าชั่วโมงปกติ** (1.0x)`, icon: 'fa-clock', color: 'amber' },  
                { title: '**เบิกเงินล่วงหน้าวันนี้**', value: `฿${totalDailyAdvance.toLocaleString()}`, sub: `**หักจากค่าแรงสุทธิ**`, icon: 'fa-hand-holding-dollar', color: 'rose' },  
                { title: '**รวมค่าแรงต้องจ่ายวันนี้**', value: `฿${totalDailyPay.toLocaleString()}`, sub: `**ประมาณการค่าแรงวันนี้**`, icon: 'fa-money-bill-wave', color: 'brand' }  
            ]);  
        }  
  
        // Calendar View  
        function renderCalendarView() {  
            const monthStr = document.getElementById('selectedMonth').value; // 'YYYY-MM'  
            const [year, month] = monthStr.split('-').map(Number);  
  
            const firstDayIndex = new Date(year, month - 1, 1).getDay();  
            const totalDays = new Date(year, month, 0).getDate();  
  
            let calendarHTML = `  
                <div class="p-6">  
                    <div class="grid grid-cols-7 gap-2 mb-2 text-center font-bold text-xs text-slate-500 uppercase">  
                        <div class="text-rose-500">**อาทิตย์**</div>  
                        <div>**จันทร์**</div>  
                        <div>**อังคาร**</div>  
                        <div>**พุธ**</div>  
                        <div>**พฤหัสบดี**</div>  
                        <div>**ศุกร์**</div>  
                        <div class="text-brand-600">**เสาร์**</div>  
                    </div>  
                    <div class="grid grid-cols-7 gap-2">  
            `;  
  
            // Blank spaces for previous month  
            for (let i = 0; i < firstDayIndex; i++) {  
                calendarHTML += `<div class="bg-slate-50/50 rounded-xl min-h-[100px] border border-slate-100 opacity-30"></div>`;  
            }  
  
            let monthTotalPresentDays = 0;  
            let monthTotalOffDays = 0;  
  
            for (let day = 1; day <= totalDays; day++) {  
                const dayStr = `${year}-${String(month).padStart(2, '0')}-${String(day).padStart(2, '0')}`;  
                const dayLogs = attendanceStore[dayStr] || {};  
                  
                let presentCount = 0;  
                let totalActiveWorkers = 0;  
  
                employees.forEach(emp => {  
                    if (isEmployeeActiveOnDate(emp, dayStr)) {  
                        totalActiveWorkers++;  
                        if (dayLogs[emp.id] && dayLogs[emp.id].isPresent) {  
                            presentCount++;  
                        }  
                    }  
                });  
  
                if (presentCount > 0) monthTotalPresentDays++;  
                else monthTotalOffDays++;  
  
                const isToday = dayStr === new Date().toISOString().split('T')[0];  
  
                let badgeColor = 'bg-slate-100 text-slate-500 border-slate-200';  
                let statusText = '**วันหยุด** / **ไม่มีคน**';  
                if (presentCount > 0) {  
                    badgeColor = 'bg-emerald-50 text-emerald-700 border-emerald-200';  
                    statusText = `**มาทำงาน** ${presentCount}/${totalActiveWorkers} **คน**`;  
                }  
  
                calendarHTML += `  
                    <div onclick="jumpToDate('${dayStr}')" class="bg-white rounded-xl p-2.5 border ${isToday ? 'border-brand-500 ring-2 ring-brand-100' : 'border-slate-200'} hover:border-brand-400 hover:shadow-md transition cursor-pointer flex flex-col justify-between min-h-[100px]">  
                        <div class="flex items-center justify-between">  
                            <span class="text-xs font-bold ${isToday ? 'bg-brand-600 text-white w-6 h-6 rounded-full flex items-center justify-center' : 'text-slate-700'}">${day}</span>  
                            ${isToday ? '<span class="text-[9px] text-brand-600 font-semibold">**วันนี้**</span>' : ''}  
                        </div>  
                        <div class="mt-2">  
                            <div class="text-[10px] font-semibold px-2 py-1 rounded-lg border text-center ${badgeColor}">  
                                ${statusText}  
                            </div>  
                        </div>  
                    </div>  
                `;  
            }  
  
            calendarHTML += `</div></div>`;  
            document.getElementById('mainContentArea').innerHTML = calendarHTML;  
  
            renderStatCards([  
                { title: '**เดือนที่เลือก**', value: `${monthStr}`, sub: `**มุมมองปฏิทินรายเดือน**`, icon: 'fa-calendar', color: 'brand' },  
                { title: '**วันที่มีคนมาทำงาน**', value: `${monthTotalPresentDays} **วัน**`, sub: `**มีปฏิบัติงานจริง**`, icon: 'fa-user-check', color: 'emerald' },  
                { title: '**วันหยุด**/**ไม่มีคนทำงาน**', value: `${monthTotalOffDays} **วัน**`, sub: `**วันหยุดประจำสัปดาห์**/**ทีม**`, icon: 'fa-mug-hot', color: 'rose' },  
                { title: '**พนักงานทั้งหมด**', value: `${employees.length} **คน**`, sub: `**กำลังพลในสังกัด**`, icon: 'fa-users', color: 'amber' }  
            ]);  
        }  
  
        function jumpToDate(dateStr) {  
            document.getElementById('selectedDate').value = dateStr;  
            switchView('daily');  
        }  
  
        // Monthly View  
        function renderMonthlyView(search, selectedSite) {  
            const monthStr = document.getElementById('selectedMonth').value;  
            document.getElementById('mainContentArea').innerHTML = `  
                <table class="w-full text-left border-collapse text-xs">  
                    <thead class="bg-slate-100 text-slate-600 uppercase font-semibold text-[11px] tracking-wider border-b border-slate-200">  
                        <tr>  
                            <th class="p-3.5">**ลูกน้อง** / **ตำแหน่ง**</th>  
                            <th class="p-3.5 text-center">**ไซต์งานทั้งหมด**</th>  
                            <th class="p-3.5 text-center">**วันมา**</th>  
                            <th class="p-3.5 text-center">**วันลา**/**หยุด**</th>  
                            <th class="p-3.5 text-center">**รวม** OT</th>  
                            <th class="p-3.5 text-right">**ค่าแรงเต็ม**</th>  
                            <th class="p-3.5 text-right text-rose-600">**รวมเบิก**</th>  
                            <th class="p-3.5 text-right font-bold text-brand-700">**คงเหลือจ่ายสุทธิ**</th>  
                        </tr>  
                    </thead>  
                    <tbody id="attendanceTableBody" class="divide-y divide-slate-100 text-slate-700 font-normal"></tbody>  
                </table>  
            `;  
  
            const filteredEmployees = employees.filter(emp => emp.name.toLowerCase().includes(search) || emp.position.toLowerCase().includes(search));  
            let totalMonthPay = 0, totalMonthAdvance = 0, totalMonthNet = 0, activeWorkerSet = new Set(), totalTeamDaysWorked = 0;  
            const tbody = document.getElementById('attendanceTableBody');  
  
            filteredEmployees.forEach(emp => {  
                let daysWorked = 0, daysOff = 0, totalOT = 0, totalBasePay = 0, totalAdvance = 0;  
                let empSiteSet = new Set();  
  
                Object.keys(attendanceStore).forEach(date => {  
                    if (date.startsWith(monthStr)) {  
                        const log = attendanceStore[date][emp.id];  
                        if (log) {  
                            const empSites = getLogSites(log);  
                            if (selectedSite === 'ALL' || empSites.includes(selectedSite)) {  
                                if (log.isPresent) {  
                                    daysWorked++;  
                                    const pay = calculatePay(emp, log);  
                                    totalOT += pay.otHours;  
                                    totalBasePay += pay.totalPay;  
                                    activeWorkerSet.add(emp.id);  
                                    totalTeamDaysWorked++;  
                                    empSites.forEach(s => empSiteSet.add(s));  
                                } else {  
                                    daysOff++;  
                                }  
                                totalAdvance += parseFloat(log.advance || 0);  
                            }  
                        }  
                    }  
                });  
  
                const netPay = totalBasePay - totalAdvance;  
                totalMonthPay += totalBasePay;  
                totalMonthAdvance += totalAdvance;  
                totalMonthNet += netPay;  
  
                const siteListArray = Array.from(empSiteSet);  
                const siteText = siteListArray.length > 0 ? `<span class="inline-block text-[10px] bg-slate-100 text-slate-700 font-semibold px-2 py-0.5 rounded-full">**รวม** ${siteListArray.length} **ไซต์**: ${siteListArray.join(', ')}</span>` : `<span class="text-slate-400">-</span>`;  
  
                const tr = document.createElement('tr');  
                tr.className = 'hover:bg-slate-50 transition border-b border-slate-100';  
                tr.innerHTML = `  
                    <td class="p-3.5"><div class="font-bold text-slate-800 text-xs">${emp.name}</div><div class="flex items-center gap-1.5 mt-0.5"><span class="inline-block text-[10px] bg-slate-100 text-slate-600 px-2 py-0.5 rounded">${emp.position}</span><span class="text-[10px] text-slate-400 font-mono">฿${emp.baseWage}/${emp.wageType === 'DAILY' ? '**วัน**' : '**ชม**.'}</span></div></td>  
                    <td class="p-3.5 text-center">${siteText}</td>  
                    <td class="p-3.5 text-center font-bold text-emerald-600">${daysWorked} **วัน**</td>  
                    <td class="p-3.5 text-center font-semibold text-slate-400">${daysOff} **วัน**</td>  
                    <td class="p-3.5 text-center font-mono font-semibold text-amber-700">${totalOT} **ชม**.</td>  
                    <td class="p-3.5 text-right font-mono font-semibold text-slate-700">฿${totalBasePay.toLocaleString()}</td>  
                    <td class="p-3.5 text-right font-mono font-bold text-rose-600">฿${totalAdvance.toLocaleString()}</td>  
                    <td class="p-3.5 text-right font-mono font-bold text-brand-700 text-sm">฿${netPay.toLocaleString()}</td>  
                `;  
                tbody.appendChild(tr);  
            });  
  
            if (filteredEmployees.length === 0) {  
                tbody.innerHTML = `<tr><td colspan="8" class="p-8 text-center text-slate-400">**ไม่พบข้อมูลประจำเดือนนี้**</td></tr>`;  
            }  
  
            renderStatCards([  
                { title: '**คนงานทำงาน** / **วันรวม**', value: `${activeWorkerSet.size} **คน** (${totalTeamDaysWorked} **วัน**-**คน**)`, sub: `**จากทั้งหมด** ${employees.length} **คน**`, icon: 'fa-users', color: 'emerald' },  
                { title: '**ยอดเบิกล่วงหน้ารวม**', value: `฿${totalMonthAdvance.toLocaleString()}`, sub: `**หักจากค่าแรงเต็ม**`, icon: 'fa-hand-holding-dollar', color: 'rose' },  
                { title: '**ค่าแรงเต็มทั้งเดือน**', value: `฿${totalMonthPay.toLocaleString()}`, sub: `**รวมปกติ** + OT`, icon: 'fa-calculator', color: 'amber' },  
                { title: '**ยอดคงเหลือจ่ายสุทธิ**', value: `฿${totalMonthNet.toLocaleString()}`, sub: `**ยอดสุทธิที่ต้องจ่าย**`, icon: 'fa-money-check-dollar', color: 'brand' }  
            ]);  
        }  
  
        function renderStatCards(cards) {  
            const container = document.getElementById('statCardsContainer');  
            container.innerHTML = '';  
            cards.forEach(c => {  
                const colorMap = {  
                    emerald: 'text-emerald-600 bg-emerald-50 border-emerald-100',  
                    amber: 'text-amber-600 bg-amber-50 border-amber-100',  
                    rose: 'text-rose-600 bg-rose-50 border-rose-100',  
                    brand: 'text-brand-600 bg-brand-50 border-brand-100'  
                };  
                const div = document.createElement('div');  
                div.className = "bg-white p-4 rounded-2xl border border-slate-200 shadow-sm flex items-center justify-between";  
                div.innerHTML = `  
                    <div>  
                        <p class="text-[11px] font-semibold text-slate-400 uppercase tracking-wider">${c.title}</p>  
                        <h3 class="text-base sm:text-lg font-bold text-slate-800 mt-0.5">${c.value}</h3>  
                        <p class="text-[10px] text-slate-500 mt-0.5">${c.sub}</p>  
                    </div>  
                    <div class="p-3 rounded-xl border ${colorMap[c.color]} flex items-center justify-center">  
                        <i class="fa-solid ${c.icon} text-lg"></i>  
                    </div>  
                `;  
                container.appendChild(div);  
            });  
        }  
  
        function updateAttendance(empId, field, value) {  
            const selectedDate = document.getElementById('selectedDate').value;  
            if (!attendanceStore[selectedDate]) attendanceStore[selectedDate] = {};  
            if (!attendanceStore[selectedDate][empId]) {  
                attendanceStore[selectedDate][empId] = { isPresent: true, sites: [sites[0] || ''], workType: 'FULL', customHours: 8, otHours: 0, advance: 0 };  
            }  
            attendanceStore[selectedDate][empId][field] = value;  
            saveData();  
            renderDashboard();  
            showToast('**อัปเดตข้อมูลบันทึกเวลาเรียบร้อย**');  
        }  
  
        function toggleEmployeeSite(empId, siteName) {  
            const selectedDate = document.getElementById('selectedDate').value;  
            if (!attendanceStore[selectedDate]) attendanceStore[selectedDate] = {};  
            if (!attendanceStore[selectedDate][empId]) {  
                attendanceStore[selectedDate][empId] = { isPresent: true, sites: [siteName], workType: 'FULL', customHours: 8, otHours: 0, advance: 0 };  
            }  
            let log = attendanceStore[selectedDate][empId];  
            let currentSites = getLogSites(log);  
            if (currentSites.includes(siteName)) {  
                if (currentSites.length > 1) currentSites = currentSites.filter(s => s !== siteName);  
            } else {  
                currentSites.push(siteName);  
            }  
            log.sites = currentSites;  
            saveData();  
            renderDashboard();  
            showToast('**ปรับปรุงไซต์งานเรียบร้อย**');  
        }  
  
        function getLogSites(log) {  
            if (!log) return [sites[0] || ''];  
            if (Array.isArray(log.sites)) return log.sites;  
            if (log.site) return [log.site];  
            return [sites[0] || ''];  
        }  
  
        function setAllHolidayToday() {  
            const selectedDate = document.getElementById('selectedDate').value;  
            if (!attendanceStore[selectedDate]) attendanceStore[selectedDate] = {};  
            employees.forEach(emp => {  
                if (isEmployeeActiveOnDate(emp, selectedDate)) {  
                    if (!attendanceStore[selectedDate][emp.id]) {  
                        attendanceStore[selectedDate][emp.id] = { isPresent: false, sites: [sites[0] || ''], workType: 'FULL', customHours: 8, otHours: 0, advance: 0 };  
                    } else {  
                        attendanceStore[selectedDate][emp.id].isPresent = false;  
                    }  
                }  
            });  
            saveData();  
            renderDashboard();  
            showToast('**ปรับเป็นวันหยุดทั้งทีมสำหรับวันนี้แล้ว**');  
        }  
  
        function isEmployeeActiveOnDate(emp, dateStr) {  
            if (emp.startDate && dateStr < emp.startDate) return false;  
            if (emp.resignDate && dateStr > emp.resignDate) return false;  
            return true;  
        }  
  
        function saveEmployee(e) {  
            e.preventDefault();  
            const editId = document.getElementById('empEditId').value;  
            const name = document.getElementById('empName').value.trim();  
            const posSelect = document.getElementById('empPositionSelect').value;  
            const posCustom = document.getElementById('empPositionCustom').value.trim();  
            const wageType = document.getElementById('empWageType').value;  
            const baseWage = parseFloat(document.getElementById('empBaseWage').value) || 0;  
            const startDate = document.getElementById('empStartDate').value;  
            const resignDate = document.getElementById('empResignDate').value;  
            const finalPosition = posSelect === 'OTHER' ? posCustom : posSelect;  
  
            if (editId) {  
                const emp = employees.find(x => x.id === editId);  
                if (emp) {  
                    emp.name = name;  
                    emp.position = finalPosition;  
                    emp.wageType = wageType;  
                    emp.baseWage = baseWage;  
                    emp.startDate = startDate;  
                    emp.resignDate = resignDate;  
                }  
            } else {  
                employees.push({ id: 'emp_' + Date.now(), name, position: finalPosition, wageType, baseWage, startDate, resignDate });  
            }  
            saveData();  
            closeModal('addEmployeeModal');  
            renderDashboard();  
            showToast('**บันทึกข้อมูลพนักงานเรียบร้อย**');  
        }  
  
        function editEmployee(id) {  
            const emp = employees.find(x => x.id === id);  
            if (!emp) return;  
            document.getElementById('empEditId').value = emp.id;  
            document.getElementById('empName').value = emp.name;  
            document.getElementById('empWageType').value = emp.wageType;  
            document.getElementById('empBaseWage').value = emp.baseWage;  
            document.getElementById('empStartDate').value = emp.startDate || '';  
            document.getElementById('empResignDate').value = emp.resignDate || '';  
            const posSelect = document.getElementById('empPositionSelect');  
            if (positions.includes(emp.position)) {  
                posSelect.value = emp.position;  
                document.getElementById('customPositionBox').classList.add('hidden');  
            } else {  
                posSelect.value = 'OTHER';  
                document.getElementById('customPositionBox').classList.remove('hidden');  
                document.getElementById('empPositionCustom').value = emp.position;  
            }  
            document.getElementById('empModalTitle').innerHTML = `<i class="fa-solid fa-user-pen text-brand-500"></i> **แก้ไขข้อมูลพนักงาน**`;  
            openModal('addEmployeeModal');  
        }  
  
        function deleteEmployee(id) {  
            if (confirm('**คุณต้องการลบข้อมูลพนักงานคนนี้ใช่หรือไม่**?')) {  
                employees = employees.filter(x => x.id !== id);  
                saveData();  
                renderDashboard();  
                showToast('**ลบพนักงานเรียบร้อยแล้ว**');  
            }  
        }  
  
        function populateSiteDropdowns() {  
            const filterSite = document.getElementById('filterSite');  
            filterSite.innerHTML = `<option value="ALL">**ไซต์งานทั้งหมด**</option>` + sites.map(s => `<option value="${s}">${s}</option>`).join('');  
            const posSelect = document.getElementById('empPositionSelect');  
            posSelect.innerHTML = positions.map(p => `<option value="${p}">${p}</option>`).join('') + `<option value="OTHER">**อื่นๆ**...</option>`;  
            renderSitesList();  
            renderPositionsList();  
        }  
  
        function toggleCustomPositionInput() {  
            const val = document.getElementById('empPositionSelect').value;  
            const customBox = document.getElementById('customPositionBox');  
            if (val === 'OTHER') customBox.classList.remove('hidden');  
            else customBox.classList.add('hidden');  
        }  
  
        function addSite() {  
            const val = document.getElementById('newSiteInput').value.trim();  
            if (val && !sites.includes(val)) {  
                sites.push(val);  
                document.getElementById('newSiteInput').value = '';  
                saveData();  
                populateSiteDropdowns();  
                renderDashboard();  
            }  
        }  
  
        function removeSite(idx) {  
            if (sites.length <= 1) return alert('**ต้องมีอย่างน้อย** 1 **ไซต์งานในระบบ**');  
            sites.splice(idx, 1);  
            saveData();  
            populateSiteDropdowns();  
            renderDashboard();  
        }  
  
        function renderSitesList() {  
            document.getElementById('sitesList').innerHTML = sites.map((s, idx) => `  
                <div class="flex items-center justify-between p-2 bg-slate-50 rounded-lg text-xs">  
                    <span class="font-medium text-slate-700">${s}</span>  
                    <button onclick="removeSite(${idx})" class="text-rose-500 hover:text-rose-700"><i class="fa-solid fa-trash"></i></button>  
                </div>  
            `).join('');  
        }  
  
        function addPosition() {  
            const val = document.getElementById('newPosInput').value.trim();  
            if (val && !positions.includes(val)) {  
                positions.push(val);  
                document.getElementById('newPosInput').value = '';  
                saveData();  
                populateSiteDropdowns();  
            }  
        }  
  
        function removePosition(idx) {  
            if (positions.length <= 1) return alert('**ต้องมีอย่างน้อย** 1 **ตำแหน่งในระบบ**');  
            positions.splice(idx, 1);  
            saveData();  
            populateSiteDropdowns();  
        }  
  
        function renderPositionsList() {  
            document.getElementById('positionsList').innerHTML = positions.map((p, idx) => `  
                <div class="flex items-center justify-between p-2 bg-slate-50 rounded-lg text-xs">  
                    <span class="font-medium text-slate-700">${p}</span>  
                    <button onclick="removePosition(${idx})" class="text-rose-500 hover:text-rose-700"><i class="fa-solid fa-trash"></i></button>  
                </div>  
            `).join('');  
        }  
  
        function exportToCSV() {  
            let csv = '\uFEFF';  
            if (currentView === 'daily') {  
                const date = document.getElementById('selectedDate').value;  
                csv += `**รายงานการลงเวลาประจำวันที่**,${date}\n**ชื่อ**-**นามสกุล**,**ตำแหน่ง**,**ไซต์งาน**,**สถานะ**,**ชม**.**ทำงาน**,**ชม**.OT,**เบิกเงิน**,**ค่าแรงรวม**\n`;  
                const dayLogs = attendanceStore[date] || {};  
                employees.forEach(emp => {  
                    const log = dayLogs[emp.id] || { isPresent: false, sites: [sites[0] || ''], workType: 'FULL', customHours: 8, otHours: 0, advance: 0 };  
                    const pay = calculatePay(emp, log);  
                    const empSites = getLogSites(log).join(' | ');  
                    csv += `"${emp.name}","${emp.position}","${empSites}","${log.isPresent ? '**มา**' : '**ลา**/**หยุด**'}",${pay.hoursWorked},${pay.otHours},${log.advance || 0},${log.isPresent ? pay.totalPay : 0}\n`;  
                });  
            } else {  
                const month = document.getElementById('selectedMonth').value;  
                csv += `**รายงานสรุปค่าแรงประจำเดือน**,${month}\n**ชื่อ**-**นามสกุล**,**ตำแหน่ง**,**ไซต์งานทั้งหมด**,**วันมา**,**วันหยุด**,**รวม** OT,**ค่าแรงเต็ม**,**รวมเบิกสะสม**,**ยอดคงเหลือจ่าย**\n`;  
                employees.forEach(emp => {  
                    let daysWorked = 0, daysOff = 0, totalOT = 0, totalBasePay = 0, totalAdvance = 0;  
                    let empSiteSet = new Set();  
                    Object.keys(attendanceStore).forEach(date => {  
                        if (date.startsWith(month)) {  
                            const log = attendanceStore[date][emp.id];  
                            if (log) {  
                                if (log.isPresent) {  
                                    daysWorked++;  
                                    const pay = calculatePay(emp, log);  
                                    totalOT += pay.otHours;  
                                    totalBasePay += pay.totalPay;  
                                    getLogSites(log).forEach(s => empSiteSet.add(s));  
                                } else {  
                                    daysOff++;  
                                }  
                                totalAdvance += parseFloat(log.advance || 0);  
                            }  
                        }  
                    });  
                    const netPay = totalBasePay - totalAdvance;  
                    csv += `"${emp.name}","${emp.position}","${Array.from(empSiteSet).join(' | ')}",${daysWorked},${daysOff},${totalOT},${totalBasePay},${totalAdvance},${netPay}\n`;  
                });  
            }  
            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });  
            const link = document.createElement('a');  
            link.href = URL.createObjectURL(blob);  
            link.download = `report_${currentView}_${Date.now()}.csv`;  
            link.click();  
        }  
  
        function openModal(id) {  
            if (id === 'addEmployeeModal' && !document.getElementById('empEditId').value) {  
                document.getElementById('empForm').reset();  
                document.getElementById('empEditId').value = '';  
                document.getElementById('empModalTitle').innerHTML = `<i class="fa-solid fa-user-plus text-brand-500"></i> **เพิ่มข้อมูลพนักงานใหม่**`;  
                toggleCustomPositionInput();  
            }  
            document.getElementById(id).classList.remove('hidden');  
        }  
  
        function closeModal(id) { document.getElementById(id).classList.add('hidden'); }  
  
        function showToast(msg) {  
            const toast = document.getElementById('toast');  
            document.getElementById('toastMsg').innerText = msg;  
            toast.classList.remove('translate-y-20', 'opacity-0');  
            setTimeout(() => { toast.classList.add('translate-y-20', 'opacity-0'); }, 2500);  
        }  
    </script>  
</body>  
</html>  
