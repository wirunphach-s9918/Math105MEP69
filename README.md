<html lang="th" class="h-full">
 <head><script src="/_sdk/telemetry_sdk.js"></script>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ระบบประกาศคะแนนสอบ</title>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstapi.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Mitr:wght@300;400;500;600;700&amp;display=swap" rel="stylesheet">
  <style>
    body {
      box-sizing: border-box;
      font-family: 'Mitr', sans-serif;
    }
  </style>
  <script src="https://cdn.tailwindcss.com/3.4.17" type="text/javascript"></script>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
  <script src="https://cdn.jsdelivr.net/npm/lucide@0.263.0/dist/umd/lucide.min.js" type="text/javascript"></script>
 </head>
 <body class="h-full">
  <div id="app" class="h-full w-full"></div>
  <script>
    const PASSWORD = "105MEP69";
    const MAX_SCORE = 20;
    
    const defaultConfig = {
      school_name: "โรงเรียนประตูชัย",
      class_name: "ชั้นประถมศึกษาปีที่ 1/5 ปีการศึกษา 2569",
      subject_name: "สาย MEP (Mini English Program)",
      teacher_name: "นางวิรัลพัชษ์ สว่างเดือน",
      background_color: "#fef3e2",
      card_color: "#fffef5",
      primary_color: "#f97316",
      text_color: "#7c2d12",
      accent_color: "#9333ea",
      font_family: "Mitr",
      font_size: 16
    };

    // ข้อมูลนักเรียนทั้งหมด
    const studentsData = {
      "22664": { name: "เด็กชายธนภัทร เภรีไพศาล", score: 13, time: "15" },
      "24240": { name: "เด็กชายนิปุณณ์ กรอบมุข", score: 13, time: "15" },
      "24241": { name: "เด็กชายชวรัตน์ โสภาวัง", score: 19, time: "15" },
      "24242": { name: "เด็กชายปวันพัฒน์ จอดสันเทียะ", score: 16, time: "15" },
      "24243": { name: "เด็กชายธรณ์ธันย์ พันธวงษ์", score: 19, time: "15" },
      "24244": { name: "เด็กชายนิปัณณ์ กรอบมุข", score: 12, time: "15" },
      "24245": { name: "เด็กชายวรกันต์ ยินดีพิช", score: 19, time: "15" },
      "24246": { name: "เด็กชายปัณณ์ โพธิ์หอม", score: 18, time: "15" },
      "22699": { name: "เด็กหญิงเรนิตา เรืองฤทธิ์", score: 16, time: "15" },
      "22703": { name: "เด็กหญิงชนัดดาท์ รัตนมณี", score: null, time: "15" },
      "23165": { name: "เด็กหญิงฐิตานันท์ อร่ามเรือง", score: 14, time: "15" },
      "23166": { name: "เด็กหญิงพิมพ์ภัชชดา รุ่งจรัสพันธุ์", score: 16, time: "15" },
      "23205": { name: "เด็กหญิงอรัชพร ปลอดภัย", score: null, time: "15" },
      "24247": { name: "เด็กหญิงปาลิดา ประเสริฐวิถี", score: 13, time: "15" },
      "24248": { name: "เด็กหญิงปาณิตา ศรีกำปัง", score: 14, time: "15" },
      "24249": { name: "เด็กหญิงปภัคร์ จตุพรภัทร์", score: 16, time: "15" },
      "24250": { name: "เด็กหญิงธรรญพรรษ เสตะปุตตะ", score: 18, time: "15" },
      "24251": { name: "เด็กหญิงไอยเรศ กุฎีแดง", score: null, time: "15" },
      "24252": { name: "เด็กหญิงอริสา ลัดดา", score: 16, time: "15" },
      "24253": { name: "เด็กหญิงกรชนก บุญสาทร์", score: 11.5, time: "15" },
      "24254": { name: "เด็กหญิงพลอยณพัชร์ มีไพฑูรย์", score: 20, time: "15" },
      "24255": { name: "เด็กหญิงวัชราภา วิมลเกษม", score: 13, time: "15" },
      "24256": { name: "เด็กหญิงฐามิกา ทองดอนน้อย", score: 17.5, time: "15" },
      "24257": { name: "เด็กหญิงณิชนันทน์ กุลบุญ", score: 13.5, time: "15" },
      "24258": { name: "เด็กหญิงปาลิดา เทพประสาน", score: 19, time: "15" },
      "24259": { name: "เด็กหญิงปุณณภา บุญประสงค์", score: 13.5, time: "15" },
      "24260": { name: "เด็กหญิงณฐพร ผุดผ่อง", score: 15.5, time: "15" },
      "24261": { name: "เด็กหญิงวชิรญาณ์ ใหญ่มาก", score: 17, time: "15" },
      "24262": { name: "เด็กหญิงภัณฑิลา กิจโสภา", score: 15, time: "15" }
    };

    let currentStudentId = null;

    async function initApp() {
      if (window.elementSdk) {
        window.elementSdk.init({
          defaultConfig,
          onConfigChange: async (config) => {
            applyConfig(config);
            if (currentStudentId) {
              renderScoresPage();
            } else {
              renderLoginPage();
            }
          },
          mapToCapabilities: (config) => ({
            recolorables: [
              {
                get: () => config.background_color || defaultConfig.background_color,
                set: (value) => {
                  config.background_color = value;
                  window.elementSdk.setConfig({ background_color: value });
                }
              },
              {
                get: () => config.card_color || defaultConfig.card_color,
                set: (value) => {
                  config.card_color = value;
                  window.elementSdk.setConfig({ card_color: value });
                }
              },
              {
                get: () => config.text_color || defaultConfig.text_color,
                set: (value) => {
                  config.text_color = value;
                  window.elementSdk.setConfig({ text_color: value });
                }
              },
              {
                get: () => config.primary_color || defaultConfig.primary_color,
                set: (value) => {
                  config.primary_color = value;
                  window.elementSdk.setConfig({ primary_color: value });
                }
              },
              {
                get: () => config.accent_color || defaultConfig.accent_color,
                set: (value) => {
                  config.accent_color = value;
                  window.elementSdk.setConfig({ accent_color: value });
                }
              }
            ],
            borderables: [],
            fontEditable: {
              get: () => config.font_family || defaultConfig.font_family,
              set: (value) => {
                config.font_family = value;
                window.elementSdk.setConfig({ font_family: value });
              }
            },
            fontSizeable: {
              get: () => config.font_size || defaultConfig.font_size,
              set: (value) => {
                config.font_size = value;
                window.elementSdk.setConfig({ font_size: value });
              }
            }
          }),
          mapToEditPanelValues: (config) => new Map([
            ["school_name", config.school_name || defaultConfig.school_name],
            ["class_name", config.class_name || defaultConfig.class_name],
            ["subject_name", config.subject_name || defaultConfig.subject_name],
            ["teacher_name", config.teacher_name || defaultConfig.teacher_name]
          ])
        });
      }

      renderLoginPage();
    }

    function applyConfig(config) {
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseSize = config.font_size || defaultConfig.font_size;
      const bgColor = config.background_color || defaultConfig.background_color;
      
      app.style.background = `linear-gradient(135deg, ${bgColor} 0%, #f5e6f0 50%, #fce7f3 100%)`;
      app.style.fontFamily = `${customFont}, sans-serif`;
      app.style.fontSize = `${baseSize}px`;
      
      document.documentElement.style.setProperty('--card-color', config.card_color || defaultConfig.card_color);
      document.documentElement.style.setProperty('--text-color', config.text_color || defaultConfig.text_color);
      document.documentElement.style.setProperty('--primary-color', config.primary_color || defaultConfig.primary_color);
      document.documentElement.style.setProperty('--accent-color', config.accent_color || defaultConfig.accent_color);
    }

    function renderLoginPage() {
      const config = window.elementSdk?.config || defaultConfig;
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseSize = config.font_size || defaultConfig.font_size;
      const cardColor = config.card_color || defaultConfig.card_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_color || defaultConfig.primary_color;
      const schoolName = config.school_name || defaultConfig.school_name;
      const className = config.class_name || defaultConfig.class_name;
      const subjectName = config.subject_name || defaultConfig.subject_name;
      const teacherName = config.teacher_name || defaultConfig.teacher_name;

      app.innerHTML = `
        <div class="h-full w-full flex items-center justify-center p-6" style="background: linear-gradient(135deg, #fef3e2 0%, #fbe8d3 50%, #fef3e2 100%); overflow-y: auto; position: relative;">
          <div style="position: absolute; top: 20px; left: 30px; font-size: 48px; opacity: 0.7; animation: float 3s ease-in-out infinite;">🎀</div>
          <div style="position: absolute; top: 100px; right: 40px; font-size: 40px; opacity: 0.6; animation: float 4s ease-in-out infinite; animation-delay: 1s;">🍭</div>
          <div style="position: absolute; bottom: 120px; left: 50px; font-size: 44px; opacity: 0.65; animation: float 3.5s ease-in-out infinite; animation-delay: 0.5s;">🍦</div>
          <div style="position: absolute; bottom: 200px; right: 60px; font-size: 42px; opacity: 0.6; animation: float 4.5s ease-in-out infinite; animation-delay: 1.5s;">🎀</div>
          <style>
            @keyframes float {
              0%, 100% { transform: translateY(0px); }
              50% { transform: translateY(-20px); }
            }
          </style>
          <div class="w-full max-w-md" style="background: ${cardColor}; border-radius: 24px; box-shadow: 0 12px 40px rgba(124, 58, 237, 0.2); padding: 48px; border: 3px solid rgba(124, 58, 237, 0.15); position: relative;">
            <div style="position: absolute; top: -15px; left: 20px; font-size: 32px;">🎀</div>
            <div style="position: absolute; top: -15px; right: 20px; font-size: 32px;">🍭</div>
            <div style="text-align: center; margin-bottom: 32px;">
              <div style="font-size: ${baseSize * 1.76}px; font-weight: 700; background: linear-gradient(135deg, #f97316 0%, #9333ea 50%, #a855f7 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; margin-bottom: 16px; font-family: ${customFont}, sans-serif; line-height: 1.3;">
                🌟 ระบบประกาศคะแนนวิชาคณิตศาสตร์สาย MEP
              </div>
              <div style="font-size: ${baseSize * 1.1}px; font-weight: 600; color: ${textColor}; margin-bottom: 8px; font-family: ${customFont}, sans-serif;">
                ${schoolName}
              </div>
              <div style="font-size: ${baseSize * 0.95}px; color: ${textColor}; opacity: 0.8; margin-bottom: 6px; font-family: ${customFont}, sans-serif;">
                ${className}
              </div>
              <div style="font-size: ${baseSize * 0.9}px; color: ${textColor}; opacity: 0.7; margin-bottom: 8px; font-family: ${customFont}, sans-serif;">
                ${subjectName}
              </div>
              <div style="font-size: ${baseSize * 0.95}px; font-weight: 700; color: ${textColor}; opacity: 0.85; font-family: ${customFont}, sans-serif;">
                ครูผู้สอน ${teacherName}
              </div>
            </div>

            <div style="margin-bottom: 24px; padding: 20px; background: linear-gradient(135deg, #fce7f3 0%, #f3e8ff 100%); border-radius: 16px; border: 2px solid #a855f7;">
              <div style="font-size: ${baseSize * 1.05}px; font-weight: 700; color: #6b21a8; margin-bottom: 12px; font-family: ${customFont}, sans-serif; display: flex; align-items: center; gap: 8px;">
                <span style="font-size: ${baseSize * 1.3}px;">ℹ️</span>
                คำแนะนำการเข้าใช้งาน
              </div>
              <div style="font-size: ${baseSize * 0.85}px; color: #7c3aed; line-height: 1.8; font-family: ${customFont}, sans-serif;">
                <div style="margin-bottom: 8px;">
                  <span style="font-weight: 600;">📌 เลขประจำตัวนักเรียน:</span> กรอก 5 หลัก เช่น 99999
                </div>
                <div style="margin-bottom: 8px;">
                  <span style="font-weight: 600;">🔐 รหัสผ่าน:</span> 105MEP69
                </div>
                <div style="margin-top: 12px; padding: 10px; background: #f3e8ff; border-radius: 8px; border-left: 3px solid #a855f7;">
                  <span style="font-weight: 600;">💡 เคล็ดลับ:</span> หากเข้าสู่ระบบไม่ได้ ให้ตรวจสอบว่ากรอกเลขประจำตัว 5 หลักถูกต้อง และใช้รหัสผ่านที่ครูแจ้ง
                </div>
              </div>
            </div>

            <form id="loginForm" style="display: flex; flex-direction: column; gap: 20px;">
              <div>
                <label for="studentId" style="display: block; font-size: ${baseSize * 0.9}px; font-weight: 600; color: ${textColor}; margin-bottom: 8px; font-family: ${customFont}, sans-serif;">
                  เลขประจำตัวนักเรียน (5 หลัก)
                </label>
                <input 
                  type="text" 
                  id="studentId" 
                  maxlength="5"
                  pattern="[0-9]{5}"
                  required
                  style="width: 100%; padding: 14px 18px; border: 2px solid #f3e8ff; border-radius: 12px; font-size: ${baseSize}px; color: ${textColor}; font-family: ${customFont}, sans-serif; box-sizing: border-box; transition: all 0.3s; background: #fffef5;"
                  placeholder="xxxxx"
                  onfocus="this.style.borderColor='${primaryColor}'; this.style.boxShadow='0 0 0 3px rgba(168, 85, 247, 0.1)'"
                  onblur="this.style.borderColor='#f3e8ff'; this.style.boxShadow='none'"
                >
              </div>

              <div>
                <label for="password" style="display: block; font-size: ${baseSize * 0.9}px; font-weight: 600; color: ${textColor}; margin-bottom: 8px; font-family: ${customFont}, sans-serif;">
                  รหัสผ่าน
                </label>
                <input 
                  type="password" 
                  id="password" 
                  required
                  style="width: 100%; padding: 14px 18px; border: 2px solid #f3e8ff; border-radius: 12px; font-size: ${baseSize}px; color: ${textColor}; font-family: ${customFont}, sans-serif; box-sizing: border-box; transition: all 0.3s; background: #fffef5;"
                  placeholder="กรอกรหัสผ่าน"
                  onfocus="this.style.borderColor='${primaryColor}'; this.style.boxShadow='0 0 0 3px rgba(168, 85, 247, 0.1)'"
                  onblur="this.style.borderColor='#f3e8ff'; this.style.boxShadow='none'"
                >
              </div>

              <div id="errorMessage" style="display: none; padding: 14px; background: linear-gradient(135deg, #fee2e2 0%, #fef0f7 100%); border-radius: 12px; color: #dc2626; font-size: ${baseSize * 0.9}px; font-family: ${customFont}, sans-serif; border: 2px solid #fca5a5;"></div>

              <button 
                type="submit"
                style="width: 100%; padding: 16px; background: linear-gradient(135deg, #f97316 0%, #9333ea 100%); color: white; border: none; border-radius: 12px; font-size: ${baseSize * 1.1}px; font-weight: 600; cursor: pointer; transition: all 0.3s; font-family: ${customFont}, sans-serif; box-shadow: 0 4px 16px rgba(249, 115, 22, 0.3);"
                onmouseover="this.style.transform='translateY(-2px)'; this.style.boxShadow='0 8px 24px rgba(249, 115, 22, 0.4)'"
                onmouseout="this.style.transform='translateY(0)'; this.style.boxShadow='0 4px 16px rgba(249, 115, 22, 0.3)'"
              >
                ✨ เข้าสู่ระบบ
              </button>
            </form>
          </div>
        </div>
      `;

      document.getElementById('loginForm').addEventListener('submit', handleLogin);
    }

    function handleLogin(e) {
      e.preventDefault();
      const studentId = document.getElementById('studentId').value;
      const password = document.getElementById('password').value;
      const errorDiv = document.getElementById('errorMessage');

      if (studentId.length !== 5 || !/^\d{5}$/.test(studentId)) {
        errorDiv.textContent = "❌ กรุณากรอกเลขประจำตัวนักเรียน 5 หลักให้ถูกต้อง";
        errorDiv.style.display = 'block';
        return;
      }

      if (password !== PASSWORD) {
        errorDiv.textContent = "❌ รหัสผ่านไม่ถูกต้อง";
        errorDiv.style.display = 'block';
        return;
      }

      if (!studentsData[studentId]) {
        errorDiv.textContent = "❌ ไม่พบข้อมูลนักเรียนในระบบ";
        errorDiv.style.display = 'block';
        return;
      }

      currentStudentId = studentId;
      renderScoresPage();
    }

    function renderScoresPage() {
      const config = window.elementSdk?.config || defaultConfig;
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseSize = config.font_size || defaultConfig.font_size;
      const cardColor = config.card_color || defaultConfig.card_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_color || defaultConfig.primary_color;
      const accentColor = config.accent_color || defaultConfig.accent_color;
      const schoolName = config.school_name || defaultConfig.school_name;
      const className = config.class_name || defaultConfig.class_name;
      const subjectName = config.subject_name || defaultConfig.subject_name;
      const teacherName = config.teacher_name || defaultConfig.teacher_name;

      const studentData = studentsData[currentStudentId];
      const score = studentData.score;
      const percentage = score !== null ? ((score / MAX_SCORE) * 100).toFixed(2) : null;

      app.innerHTML = `
        <div class="h-full w-full" style="background: linear-gradient(135deg, #fef3e2 0%, #fbe8d3 50%, #fef3e2 100%); overflow-y: auto; position: relative;">
          <div style="position: fixed; top: 40px; left: 30px; font-size: 48px; opacity: 0.5; animation: float 3s ease-in-out infinite; pointer-events: none;">🍦</div>
          <div style="position: fixed; top: 150px; right: 50px; font-size: 44px; opacity: 0.55; animation: float 4s ease-in-out infinite; animation-delay: 1s; pointer-events: none;">🎀</div>
          <div style="position: fixed; bottom: 300px; left: 40px; font-size: 40px; opacity: 0.5; animation: float 3.5s ease-in-out infinite; animation-delay: 0.5s; pointer-events: none;">🍭</div>
          <div style="position: fixed; bottom: 400px; right: 60px; font-size: 42px; opacity: 0.55; animation: float 4.5s ease-in-out infinite; animation-delay: 1.5s; pointer-events: none;">🎀</div>
          <style>
            @keyframes float {
              0%, 100% { transform: translateY(0px); }
              50% { transform: translateY(-20px); }
            }
          </style>
          <div style="max-width: 900px; margin: 0 auto; padding: 32px 24px;">
            <div style="background: ${cardColor}; border-radius: 24px; padding: 36px; box-shadow: 0 8px 32px rgba(168, 85, 247, 0.15); margin-bottom: 28px; border: 3px solid rgba(168, 85, 247, 0.1);">
              <div style="display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 16px; margin-bottom: 20px; position: relative;">
                <div style="position: absolute; top: -20px; left: 10px; font-size: 28px;">🍭</div>
                <div style="position: absolute; top: -15px; right: 10px; font-size: 28px;">🍦</div>
                <div>
                  <div style="font-size: ${baseSize * 1.9}px; font-weight: 700; background: linear-gradient(135deg, #f97316 0%, #9333ea 50%, #a855f7 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; margin-bottom: 10px; font-family: ${customFont}, sans-serif;">
                    💖 คะแนนสอบ บทที่ 1
                  </div>
                  <div style="font-size: ${baseSize * 1.05}px; font-weight: 600; color: ${textColor}; margin-bottom: 5px; font-family: ${customFont}, sans-serif;">
                    ${schoolName}
                  </div>
                  <div style="font-size: ${baseSize * 0.95}px; color: ${textColor}; opacity: 0.75; margin-bottom: 3px; font-family: ${customFont}, sans-serif;">
                    ${className}
                  </div>
                  <div style="font-size: ${baseSize * 0.9}px; color: ${textColor}; opacity: 0.7; margin-bottom: 5px; font-family: ${customFont}, sans-serif;">
                    ${subjectName}
                  </div>
                  <div style="font-size: ${baseSize * 0.95}px; font-weight: 700; color: ${textColor}; opacity: 0.85; font-family: ${customFont}, sans-serif;">
                    ครูผู้สอน ${teacherName}
                  </div>
                </div>
                <button 
                  id="logoutBtn"
                  style="padding: 12px 24px; background: white; color: #f97316; border: 2px solid #f97316; border-radius: 12px; font-size: ${baseSize * 0.9}px; font-weight: 600; cursor: pointer; font-family: ${customFont}, sans-serif; transition: all 0.3s;"
                  onmouseover="this.style.background='linear-gradient(135deg, #f97316 0%, #9333ea 100%)'; this.style.color='white'; this.style.transform='scale(1.05)'"
                  onmouseout="this.style.background='white'; this.style.color='#f97316'; this.style.transform='scale(1)'"
                >
                  👋 ออกจากระบบ
                </button>
              </div>
              <div style="padding: 20px; background: linear-gradient(135deg, #f97316 0%, #9333ea 50%, #a855f7 100%); border-radius: 16px; box-shadow: 0 4px 16px rgba(249, 115, 22, 0.3); margin-bottom: 16px;">
                <div style="font-size: ${baseSize * 0.9}px; color: white; opacity: 0.95; margin-bottom: 6px; font-family: ${customFont}, sans-serif;">
                  ⭐ เลขประจำตัวนักเรียน
                </div>
                <div style="font-size: ${baseSize * 1.6}px; font-weight: 700; color: white; font-family: ${customFont}, sans-serif;">
                  ${currentStudentId}
                </div>
              </div>
              <div style="padding: 18px; background: linear-gradient(135deg, #fce7f3 0%, #f3e8ff 100%); border-radius: 16px; border: 2px solid rgba(168, 85, 247, 0.2);">
                <div style="font-size: ${baseSize * 1.25}px; font-weight: 600; color: ${textColor}; font-family: ${customFont}, sans-serif;">
                  👤 ${studentData.name}
                </div>
              </div>
            </div>

            <div style="background: ${cardColor}; border-radius: 20px; padding: 28px; box-shadow: 0 6px 24px rgba(168, 85, 247, 0.1); border: 3px solid rgba(168, 85, 247, 0.2); position: relative;">
              <div style="position: absolute; top: -18px; left: 30px; font-size: 32px;">🎀</div>
              <div style="position: absolute; top: -18px; right: 30px; font-size: 32px;">🎀</div>
                  <div style="font-size: ${baseSize * 1.3}px; font-weight: 700; color: ${textColor}; margin-bottom: 24px; font-family: ${customFont}, sans-serif;">
                📊 คะแนนสอบ บทที่ 1 เรื่อง จำนวนนับ 1 ถึง 10 และ 0
              </div>
              ${score !== null ? `
                <div style="display: flex; align-items: baseline; gap: 10px; margin-bottom: 24px;">
                  <div style="font-size: ${baseSize * 3.2}px; font-weight: 700; background: linear-gradient(135deg, #f97316 0%, #9333ea 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; font-family: ${customFont}, sans-serif;">
                    ${score}
                  </div>
                  <div style="font-size: ${baseSize * 1.5}px; color: ${textColor}; opacity: 0.6; font-family: ${customFont}, sans-serif;">
                    / ${MAX_SCORE}
                  </div>
                </div>
                
                <div style="padding: 20px; background: linear-gradient(135deg, #f3e8ff 0%, #fce7f3 100%); border-radius: 16px; border: 2px solid ${primaryColor}; margin-bottom: 24px;">
                  <div style="font-size: ${baseSize * 1.1}px; font-weight: 700; color: ${textColor}; margin-bottom: 16px; font-family: ${customFont}, sans-serif; display: flex; align-items: center; gap: 8px;">
                    <span style="font-size: ${baseSize * 1.3}px;">📈</span>
                    เปรียบเทียบกับคะแนนเฉลี่ยของชั้น
                  </div>
                <div style="display: flex; gap: 20px; align-items: center;">
                    <div style="flex: 1;">
                      <div style="display: flex; justify-content: space-between; margin-bottom: 8px;">
                        <span style="font-size: ${baseSize * 0.9}px; font-weight: 600; color: ${textColor}; font-family: ${customFont}, sans-serif;">คะแนนของหนู</span>
                        <span style="font-size: ${baseSize * 0.9}px; font-weight: 700; color: #f97316; font-family: ${customFont}, sans-serif;">${score}</span>
                      </div>
                      <div style="width: 100%; height: 12px; background: #fef3c7; border-radius: 6px; overflow: hidden;">
                        <div style="height: 100%; background: linear-gradient(90deg, #f97316 0%, #9333ea 100%); width: ${(score / MAX_SCORE) * 100}%; transition: width 0.5s;"></div>
                      </div>
                    </div>
                  </div>
                <div style="display: flex; gap: 20px; align-items: center;">
                    <div style="flex: 1;">
                      <div style="display: flex; justify-content: space-between; margin-bottom: 8px;">
                        <span style="font-size: ${baseSize * 0.9}px; font-weight: 600; color: ${textColor}; font-family: ${customFont}, sans-serif;">คะแนนเฉลี่ยชั้น</span>
                        <span style="font-size: ${baseSize * 0.9}px; font-weight: 700; color: #f97316; font-family: ${customFont}, sans-serif;">15.79</span>
                      </div>
                      <div style="width: 100%; height: 12px; background: #fef3c7; border-radius: 6px; overflow: hidden;">
                        <div style="height: 100%; background: linear-gradient(90deg, #fcd34d 0%, #f97316 100%); width: ${(15.79 / MAX_SCORE) * 100}%; transition: width 0.5s;"></div>
                      </div>
                    </div>
                  </div>
                  <div style="margin-top: 14px; padding: 12px; background: white; border-radius: 8px; text-align: center; border: 1px solid #fde68a;">
                    <div style="font-size: ${baseSize * 0.9}px; color: ${textColor}; font-family: ${customFont}, sans-serif;">
                      ${score > 15.79 ? `
                        <span style="font-weight: 700; color: #059669;">✨ หนูสูงกว่าคะแนนเฉลี่ย ${(score - 15.79).toFixed(2)} คะแนน!</span>
                      ` : score < 15.79 ? `
                        <span style="font-weight: 600; color: #d97706;">💪 หนูต่ำกว่าเฉลี่ย ${(15.79 - score).toFixed(2)} คะแนน ลองตั้งใจเพิ่มเติมนะ</span>
                      ` : `
                        <span style="font-weight: 700; color: #2563eb;">⭐ หนูได้คะแนนเท่ากับเฉลี่ยชั้น</span>
                      `}
                    </div>
                  </div>
                </div>
                <div style="padding: 12px 16px; background: linear-gradient(135deg, #dbeafe 0%, #fef3c7 100%); border-radius: 10px; margin-bottom: 18px; border: 2px solid rgba(37, 99, 235, 0.2);">
                  <div style="font-size: ${baseSize * 0.85}px; color: #1e40af; font-family: ${customFont}, sans-serif; display: flex; align-items: center; gap: 6px;">
                    <span style="font-size: ${baseSize * 1.1}px;">✅</span>
                    คะแนนทั้งหมด: <span style="font-weight: 700;">${MAX_SCORE}</span>
                  </div>
                </div>
                <div style="width: 100%; height: 14px; background: linear-gradient(90deg, #fef3c7 0%, #dbeafe 100%); border-radius: 8px; overflow: hidden; box-shadow: inset 0 2px 4px rgba(0,0,0,0.1); margin-bottom: 20px;">
                  <div style="height: 100%; background: linear-gradient(90deg, #f97316 0%, #9333ea 100%); width: ${percentage}%; transition: width 0.5s; box-shadow: 0 2px 8px rgba(249, 115, 22, 0.4);"></div>
                </div>
                ${(() => {
                  const percent = parseFloat(percentage);
                  let emoji = '';
                  let title = '';
                  let message = '';
                  
                  if (percent >= 80) {
                    emoji = '🌟';
                    title = 'ยอดเยี่ยมมาก!';
                    message = 'ผลงานของหนูแสดงให้เห็นถึงความตั้งใจและความเข้าใจที่ดีมาก ขอให้รักษามาตรฐานแบบนี้ไว้ และพัฒนาศักยภาพของตนเองต่อไป ครูเชื่อมั่นในความสามารถของหนูนะ';
                  } else if (percent >= 60) {
                    emoji = '💪';
                    title = 'ทำได้ดีขึ้นมากแล้ว';
                    message = 'ความพยายามของหนูเริ่มเห็นผลชัดเจนแล้ว ลองตั้งใจอีกนิด ฝึกฝนอย่างสม่ำเสมอ หนูจะก้าวไปถึงระดับที่สูงขึ้นได้แน่นอน';
                  } else if (percent >= 40) {
                    emoji = '✨';
                    title = 'หนูกำลังอยู่ระหว่างการพัฒนา';
                    message = 'อย่าท้อใจนะ ความก้าวหน้าเริ่มต้นจากการลงมือทำ ลองฝึกโจทย์ให้มากขึ้น ทบทวนสิ่งที่เรียนอย่างสม่ำเสมอ ครูพร้อมช่วยหนูเสมอ';
                  } else {
                    emoji = '🌱';
                    title = 'ทุกคนเรียนรู้ได้ในจังหวะของตนเอง';
                    message = 'ขอให้หนูเริ่มต้นจากการตั้งใจเรียน ทบทวนทีละนิด และฝึกอย่างต่อเนื่อง ความสำเร็จจะค่อย ๆ เกิดขึ้น ครูเชื่อว่าหนูทำได้';
                  }
                  
                  return `
                    <div style="padding: 16px; background: linear-gradient(135deg, #fef3c7 0%, #fed7aa 100%); border-radius: 12px; border: 2px solid rgba(37, 99, 235, 0.3);">
                      <div style="font-size: ${baseSize * 1.1}px; font-weight: 700; color: #92400e; margin-bottom: 8px; font-family: ${customFont}, sans-serif; display: flex; align-items: center; gap: 6px;">
                        <span style="font-size: ${baseSize * 1.3}px;">${emoji}</span>
                        ${title}
                      </div>
                      <div style="font-size: ${baseSize * 0.85}px; color: #78350f; line-height: 1.6; font-family: ${customFont}, sans-serif;">
                        ${message}
                      </div>
                    </div>
                  `;
                })()}
              ` : `
                <div style="padding: 40px 0; text-align: center;">
                  <div style="font-size: ${baseSize * 2.5}px; margin-bottom: 12px;">😢</div>
                  <div style="font-size: ${baseSize * 1.3}px; font-weight: 700; color: #dc2626; font-family: ${customFont}, sans-serif;">
                    ขาดสอบ
                  </div>
                </div>
              `}
            </div>
          </div>
        </div>
      `;

      document.getElementById('logoutBtn').addEventListener('click', () => {
        currentStudentId = null;
        renderLoginPage();
      });
    }

    initApp();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'a09fae9da8039e40',t:'MTc4MTE3MTExNw=='};var a=document.createElement('script');a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
