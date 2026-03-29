<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>承峰不動產 - 永義成功莊敬店 建檔系統</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <style>
        /* =========================================
           1. 品牌視覺與全域變數
           ========================================= */
        :root {
            --brand-yellow: #FDB913;
            --brand-green: #008C4A; 
            --brand-red: #D82A29;   
            --brand-dark: #001F5B;  
            --bg-color: #F4F6F8;
            --card-radius: 16px;
            --input-radius: 12px;
        }

        * { box-sizing: border-box; font-family: 'PingFang TC', 'Helvetica Neue', Helvetica, Arial, sans-serif; }
        body { background-color: var(--bg-color); color: #333; margin: 0; padding: 20px 15px 120px; line-height: 1.5; }
        .container { max-width: 800px; margin: 0 auto; }

        .header { text-align: center; margin-bottom: 25px; }
        .header h1 { color: var(--brand-green); font-size: 24px; margin: 0; font-weight: 900; letter-spacing: 1px;}
        .header p { color: #7A7571; font-size: 14px; margin-top: 6px; }

        /* =========================================
           2. 頂部選單
           ========================================= */
        .category-selector { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-bottom: 30px; }
        
        .cat-btn {
            background: #FFFFFF; border: 2px solid #E4E4E4; border-radius: var(--card-radius);
            padding: 20px 10px; text-align: center; cursor: pointer; transition: all 0.2s;
            box-shadow: 0 4px 12px rgba(0,0,0,0.03); display: flex; flex-direction: column; align-items: center; justify-content: center;
        }
        .cat-btn:hover { border-color: var(--brand-yellow); transform: translateY(-3px); }
        .cat-btn.active { border-color: var(--brand-green); background-color: rgba(0,140,74,0.05); box-shadow: 0 4px 16px rgba(0,140,74,0.15); }
        .cat-icon { font-size: 32px; margin-bottom: 8px; }
        .cat-title { font-size: 16px; font-weight: bold; color: var(--brand-dark); }
        .cat-disabled { opacity: 0.5; cursor: not-allowed; position: relative; }
        .cat-disabled:hover { transform: none; border-color: #E4E4E4; }
        .badge-soon { position: absolute; top: -10px; right: -10px; background: var(--brand-red); color: white; font-size: 12px; padding: 2px 8px; border-radius: 20px; font-weight: bold; }

        #mainFormArea { display: none; animation: fadeIn 0.4s ease; }

        /* =========================================
           3. 表單介面樣式 (電腦版預設)
           ========================================= */
        .card {
            background: #FFFFFF; border-radius: var(--card-radius); padding: 24px 20px;
            margin-bottom: 24px; box-shadow: 0 8px 24px rgba(0, 0, 0, 0.04);
            border-top: 4px solid var(--brand-yellow);
        }
        .card.internal-card { border-top: 4px solid var(--brand-red); background: #FFF9F9; }
        .card.internal-card .card-title { color: var(--brand-red); border-bottom-color: #FFEDED;}

        .card-title { font-size: 18px; font-weight: 900; margin-top: 0; margin-bottom: 20px; color: var(--brand-green); border-bottom: 2px solid #F0F0F0; padding-bottom: 12px; }

        .form-group { margin-bottom: 18px; position: relative;}
        label { display: block; font-size: 14px; font-weight: 700; margin-bottom: 8px; color: #4A4A4A; }
        .required::after { content: " *"; color: var(--brand-red); }

        input[type="text"], input[type="number"], textarea, select, input[type="file"] {
            width: 100%; padding: 14px 16px; border: 1.5px solid #E4E4E4;
            border-radius: var(--input-radius); font-size: 16px; transition: all 0.2s ease; background-color: #FAFAFA; box-sizing: border-box;
        }
        input:focus, textarea:focus, select:focus { outline: none; border-color: var(--brand-green); background-color: #FFFFFF; box-shadow: 0 0 0 4px rgba(0, 140, 74, 0.1); }
        .input-error { border-color: var(--brand-red) !important; background-color: #FFF0F0 !important; }

        .chip-group { display: flex; flex-wrap: wrap; gap: 10px; }
        .chip-group input[type="radio"], .chip-group input[type="checkbox"] { display: none; }
        .chip-label { padding: 10px 16px; border: 1.5px solid #E4E4E4; border-radius: 30px; font-size: 15px; font-weight: 500; cursor: pointer; transition: all 0.2s; background-color: #FFFFFF; color: #666; }
        .chip-group input:checked + .chip-label { background-color: var(--brand-green); color: #FFFFFF; border-color: var(--brand-green); box-shadow: 0 4px 10px rgba(0, 140, 74, 0.2); }

        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
        .grid-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 16px; }
        
        .other-text-input { display: none; margin-top: 10px; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(-5px); } to { opacity: 1; transform: translateY(0); } }
        
        .fill-blank-group { display: flex; align-items: center; gap: 8px; margin-top: 5px; flex-wrap: wrap; }
        .fill-blank-group input { padding: 10px; text-align: center; }
        .fill-blank-text { font-weight: bold; color: #555; }

        .btn-add-tool { background: rgba(0, 140, 74, 0.08); color: var(--brand-green); border: 1px dashed var(--brand-green); padding: 8px 15px; border-radius: 8px; font-size: 14px; font-weight: bold; cursor: pointer; margin-top: 5px; width: 100%; transition: all 0.2s; }
        .btn-add-tool:hover { background: rgba(0, 140, 74, 0.15); }
        .dynamic-row { display: flex; gap: 10px; margin-top: 10px; animation: fadeIn 0.3s; }
        .dynamic-row .dyn-key { width: 35%; }
        .dynamic-row .dyn-val { flex: 1; }
        .btn-remove-row { background: none; border: none; color: var(--brand-red); font-size: 20px; cursor: pointer; padding: 0 5px;}

        /* =========================================
           ✨ 3.1 手機版專屬最佳化 (解決畫面太大、一格一格填)
           ========================================= */
        @media (max-width: 600px) {
            body { padding: 15px 10px 100px; }
            .category-selector { grid-template-columns: repeat(2, 1fr); gap: 10px; margin-bottom: 20px; }
            .cat-btn { padding: 15px 8px; }
            
            /* 縮小卡片留白，讓畫面更緊湊 */
            .card { padding: 16px 15px; margin-bottom: 15px; border-radius: 12px; }
            .card-title { font-size: 16px; margin-bottom: 15px; padding-bottom: 8px; }
            
            /* 強制所有欄位變成「上下排 (一格一格)」 */
            .grid-2, .grid-3 { grid-template-columns: 1fr; gap: 12px; }
            
            /* 縮小輸入框的內距，節省高度 */
            .form-group { margin-bottom: 15px; }
            label { margin-bottom: 6px; font-size: 13px; }
            input[type="text"], input[type="number"], textarea, select, input[type="file"] {
                padding: 12px 14px; 
            }
            .chip-label { padding: 8px 14px; font-size: 14px; }
        }

        /* =========================================
           4. 底部懸浮按鈕 & Modal
           ========================================= */
        .fixed-bottom-bar { position: fixed; bottom: 0; left: 0; width: 100%; background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); padding: 15px 20px; box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.08); display: flex; justify-content: center; z-index: 100; }
        .submit-btn { width: 100%; max-width: 600px; background-color: var(--brand-green); color: white; border: none; padding: 16px; font-size: 18px; font-weight: bold; border-radius: 50px; cursor: pointer; box-shadow: 0 6px 16px rgba(0, 140, 74, 0.25); }

        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); z-index: 2000; justify-content: center; align-items: center; padding: 20px; opacity: 0; transition: opacity 0.3s ease; }
        .modal.show { display: flex; opacity: 1; }
        .modal-content { background: var(--bg-color); padding: 20px; border-radius: 16px; width: 100%; max-width: 600px; max-height: 90vh; overflow-y: auto; text-align: center; }
        .modal-content h3 { margin-top: 0; color: var(--brand-dark); font-weight: 900;}
        #previewImg { width: 100%; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.2); }
        .modal-actions { display: flex; flex-wrap: wrap; gap: 10px; margin-top: 20px; }
        .modal-actions button { flex: 1; min-width: 45%; padding: 14px; border: none; border-radius: 12px; font-size: 15px; font-weight: bold; cursor: pointer; }
        .btn-back { background: #E0E0E0; color: #333; }
        .btn-share { background: var(--brand-yellow); color: var(--brand-dark); }
        .btn-line { background: #06C755; color: white; width: 100%; margin-top: 5px;}

        /* =========================================
           5. 隱藏輸出排版區 (圖片視覺)
           ========================================= */
        .export-wrapper {
            position: absolute; left: -9999px; top: 0; width: 800px; 
            background: radial-gradient(circle at 10% 0%, rgba(253, 185, 19, 0.08) 0%, transparent 50%), radial-gradient(circle at 90% 100%, rgba(0, 140, 74, 0.05) 0%, transparent 50%), #FDFBF7;
            padding: 20px; box-sizing: border-box;
        }
        .export-wrapper.capturing { position: relative; left: 0; z-index: -1; }
        #capture-area { background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(30px); width: 100%; border: 1px solid rgba(255, 255, 255, 0.6); padding-bottom: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        
        .preview-header { display: flex; align-items: center; padding: 15px 20px; background: white; border-bottom: 3px solid var(--brand-yellow);}
        .preview-type { margin-left: auto; font-size: 18px; font-weight: 900; color: var(--brand-dark);}
        .preview-title { background: linear-gradient(135deg, #a8e0b3, #82d392); color: white; font-size: 26px; font-weight: 900; padding: 12px 20px; text-shadow: 1px 1px 2px rgba(0,0,0,0.1); letter-spacing: 1px; }

        .top-section { display: flex; padding: 20px; gap: 20px; }
        .summary-col { flex: 1; display: flex; flex-direction: column; justify-content: space-between; }
        .data-item { border-bottom: 1px solid #eee; padding-bottom: 8px; }
        .data-label { font-size: 14px; color: #666; font-weight: bold; }
        .data-value { color: var(--brand-red); font-weight: 900; font-size: 38px; line-height: 1.1; }
        .data-value span { font-size: 18px; font-weight: normal; color: #333;}
        .photo-col { flex: 2; display: flex; gap: 10px; }
        .photo-col img { width: 50%; height: 220px; object-fit: cover; border: 1px solid #ddd; border-radius: 4px; background: #eee;}

        .table-section { padding: 0 20px; display: grid; grid-template-columns: 1fr 1fr; gap: 0 25px; }
        .table-header { font-weight: 900; border-bottom: 2px solid var(--brand-green); padding: 5px 0; margin-bottom: 8px; font-size: 16px; color: var(--brand-green);}
        .table-row { display: flex; border-bottom: 1px solid #eee; padding: 8px 0; font-size: 14px; }
        .table-row .th { width: 85px; color: #777; font-weight: bold;}
        .table-row .td { flex: 1; font-weight: 900; color: #222; }

        .features-section { padding: 20px; }
        .features-content { font-size: 15px; line-height: 1.6; white-space: pre-wrap; font-weight: bold; color: var(--brand-dark);}
    </style>
</head>
<body>

<div id="previewModal" class="modal">
    <div class="modal-content">
        <h3>圖片已生成</h3>
        <p style="font-size:13px; color:#666; margin-top:-10px;">(可直接長按下方圖片儲存)</p>
        <img id="previewImg" src="" alt="預覽圖">
        <div class="modal-actions">
            <button class="btn-back" id="backBtn">返回修改</button>
            <button class="btn-share" id="confirmDownloadBtn">分享圖片</button>
            <button class="btn-line" id="copyLineBtn">複製 LINE 文案 (詳細純文字)</button>
        </div>
    </div>
</div>

<div class="container">
    <div class="header">
        <h1>承峰不動產 - 物件明細建檔</h1>
        <p>請先選擇建檔類別，以載入專屬表單</p>
    </div>

    <div class="category-selector" id="catSelector">
        <div class="cat-btn" data-type="大樓"><div class="cat-icon">🏢</div><div class="cat-title">大樓</div></div>
        <div class="cat-btn" data-type="別墅"><div class="cat-icon">🏡</div><div class="cat-title">別墅</div></div>
        <div class="cat-btn cat-disabled"><span class="badge-soon">即將推出</span><div class="cat-icon">🌾</div><div class="cat-title">農地</div></div>
        <div class="cat-btn cat-disabled"><span class="badge-soon">即將推出</span><div class="cat-icon">🏗️</div><div class="cat-title">建地</div></div>
    </div>

    <form id="mainFormArea">
        <div class="card">
            <h2 class="card-title">核心資訊</h2>
            <div class="form-group"><label class="required" for="caseName">案名 (將作為主標題)</label><input type="text" id="caseName" class="req-input" placeholder="例：棒球場享未來大三房改超大兩房｜含平面車位"></div>
            
            <div class="grid-2">
                <div class="form-group"><label class="required" for="price">售價 (萬)</label><input type="text" inputmode="decimal" id="price" class="req-input format-num" placeholder="例：1,198"></div>
                
                <div class="form-group">
                    <label class="required">格局</label>
                    <div class="fill-blank-group" style="margin-bottom: 10px;">
                        <input type="number" id="layoutRoom" style="width: 50px; padding: 10px 5px;"> <span class="fill-blank-text">房</span>
                        <input type="number" id="layoutLiving" style="width: 50px; padding: 10px 5px;"> <span class="fill-blank-text">廳</span>
                        <input type="number" id="layoutBath" style="width: 50px; padding: 10px 5px;"> <span class="fill-blank-text">衛</span>
                        <input type="number" id="layoutBalcony" style="width: 50px; padding: 10px 5px;"> <span class="fill-blank-text">陽</span>
                    </div>
                    <input type="text" id="layoutOther" placeholder="或自行輸入 (例：開放空間、無隔間)">
                </div>
            </div>

            <div class="grid-2">
                <div class="form-group"><label>上傳照片 1 (主圖)</label><input type="file" accept="image/*" id="uploadImg1"></div>
                <div class="form-group"><label>上傳照片 2 (次圖)</label><input type="file" accept="image/*" id="uploadImg2"></div>
            </div>
        </div>

        <div class="card">
            <h2 class="card-title">面積與車位</h2>
            <div class="grid-3">
                <div class="form-group"><label class="required" for="totalArea">權狀坪數</label><input type="text" id="totalArea" class="req-input" placeholder="例：43.85"></div>
                <div class="form-group"><label for="mainArea">主建物</label><input type="text" id="mainArea"></div>
                <div class="form-group"><label for="subArea">附屬建物</label><input type="text" id="subArea"></div>
                <div class="form-group"><label for="publicArea">公設</label><input type="text" id="publicArea"></div>
                <div class="form-group"><label for="parkingArea">車位坪數</label><input type="text" id="parkingArea"></div>
            </div>

            <div class="form-group">
                <label>車位詳情</label>
                <div class="chip-group" style="margin-bottom: 10px;">
                    <input type="radio" name="parkKind" id="pk1" value="平面車位"><label class="chip-label" for="pk1">平面車位</label>
                    <input type="radio" name="parkKind" id="pk2" value="機械車位"><label class="chip-label" for="pk2">機械車位</label>
                </div>
                
                <div class="fill-blank-group" style="margin-bottom: 10px;">
                    <span class="fill-blank-text">位於地下</span>
                    <input type="text" id="parkAt" style="width: 60px;">
                    <span class="fill-blank-text">層</span>
                    <input type="text" id="parkNo" style="width: 80px;">
                    <span class="fill-blank-text">號 / 總共地下</span>
                    <input type="text" id="parkTotal" style="width: 60px;">
                    <span class="fill-blank-text">層</span>
                </div>

                <input type="text" id="parkOther" placeholder="其他車位狀況 (如: 一樓車位、無車位等，手動輸入)">
            </div>
        </div>

        <div class="card">
            <h2 class="card-title">建築與戶型</h2>
            <div class="grid-2">
                <div class="form-group">
                    <label>樓層</label>
                    <div class="fill-blank-group" style="margin-bottom: 10px;">
                        <span class="fill-blank-text">位於</span>
                        <input type="text" id="floorAt" style="width: 70px;">
                        <span class="fill-blank-text">樓 / 總共</span>
                        <input type="text" id="floorTotal" style="width: 70px;">
                        <span class="fill-blank-text">樓</span>
                    </div>
                    <input type="text" id="floorOther" placeholder="其他樓層狀況 (如: 整棟、手動輸入)">
                </div>

                <div class="form-group"><label for="unitType">戶型</label><input type="text" id="unitType" placeholder="例：13樓A5戶、或無"></div>
                <div class="form-group"><label for="totalHouseholds">總戶數</label><input type="text" id="totalHouseholds" placeholder="例：170戶"></div>
                <div class="form-group"><label for="manageFee">管理費</label><input type="text" id="manageFee" placeholder="例：尚未定案"></div>
            </div>

            <div class="form-group">
                <label>瓦斯</label>
                <div class="chip-group">
                    <input type="radio" name="gas" id="gas1" value="天然瓦斯" checked><label class="chip-label" for="gas1">天然瓦斯</label>
                    <input type="radio" name="gas" id="gas2" value="桶裝瓦斯"><label class="chip-label" for="gas2">桶裝瓦斯</label>
                    <input type="radio" name="gas" id="gas3" value="無瓦斯"><label class="chip-label" for="gas3">無瓦斯</label>
                    <input type="radio" name="gas" id="gas4" value="其他"><label class="chip-label" for="gas4">其他(自行輸入)</label>
                </div>
                <input type="text" id="gasOther" class="other-text-input" placeholder="請輸入其他瓦斯種類">
            </div>
            
            <div id="dynamic-container-building"></div>
            <button type="button" class="btn-add-tool" onclick="addDynamicField('dynamic-container-building', 'table')">➕ 新增表格自訂欄位</button>
        </div>

        <div class="card">
            <h2 class="card-title">法規與登記</h2>
            <div class="grid-2">
                <div class="form-group">
                    <label>屋齡 / 建築完成日</label>
                    <div class="fill-blank-group" style="margin-bottom: 12px;">
                        <span class="fill-blank-text">屋齡：</span>
                        <input type="text" id="buildingAge" style="width: 80px;" placeholder="手填">
                        <span class="fill-blank-text">年</span>
                    </div>
                    <div class="fill-blank-group">
                        <span class="fill-blank-text">完工：</span>
                        <select id="buildYear" style="width:100px;"></select> <span class="fill-blank-text">年</span>
                        <select id="buildMonth" style="width:70px;"></select> <span class="fill-blank-text">月</span>
                        <select id="buildDay" style="width:70px;"></select> <span class="fill-blank-text">日</span>
                    </div>
                </div>

                <div class="form-group">
                    <label>登記原因</label>
                    <select id="regReason" onchange="toggleSelectOtherSimple(this, 'regReasonOther')">
                        <option value="買賣">買賣</option>
                        <option value="贈與">贈與</option>
                        <option value="其他">其他(自行輸入)</option>
                    </select>
                    <input type="text" id="regReasonOther" class="other-text-input" placeholder="請輸入登記原因">
                </div>
                
                <div class="form-group">
                    <label>主要建材</label>
                    <select id="mainMaterial" onchange="toggleSelectOtherSimple(this, 'materialOther')">
                        <option value="鋼筋混凝土RC">鋼筋混凝土RC</option>
                        <option value="磚造">磚造</option>
                        <option value="加強磚造">加強磚造</option>
                        <option value="其他">其他(自行輸入)</option>
                    </select>
                    <input type="text" id="materialOther" class="other-text-input" placeholder="請輸入建材">
                </div>
                <div class="form-group"><label for="mainUsage">主要用途</label><input type="text" id="mainUsage" value="集合住宅" placeholder="手動輸入"></div>
                <div class="form-group">
                    <label>使用分區</label>
                    <select id="zoning" onchange="toggleSelectOtherSimple(this, 'zoningOther')">
                        <option value="住宅區">住宅區</option>
                        <option value="商業區">商業區</option>
                        <option value="甲種建築用地">甲種建築用地</option>
                        <option value="乙種建築用地">乙種建築用地</option>
                        <option value="丙種建築用地">丙種建築用地</option>
                        <option value="丁種建築用地">丁種建築用地</option>
                        <option value="都計內乙工">都計內乙工</option>
                        <option value="其他">其他(自行輸入)</option>
                    </select>
                    <input type="text" id="zoningOther" class="other-text-input" placeholder="請輸入使用分區">
                </div>
            </div>
        </div>

        <div class="card">
            <h2 class="card-title">公共設施</h2>
            <div class="form-group">
                <div class="chip-group">
                    <input type="checkbox" name="facility" id="f1" value="大廳"><label class="chip-label" for="f1">大廳</label>
                    <input type="checkbox" name="facility" id="f2" value="中庭花園"><label class="chip-label" for="f2">中庭花園</label>
                    <input type="checkbox" name="facility" id="f3" value="交誼廳"><label class="chip-label" for="f3">交誼廳</label>
                    <input type="checkbox" name="facility" id="f4" value="視聽室"><label class="chip-label" for="f4">視聽室</label>
                    <input type="checkbox" name="facility" id="f5" value="卡拉OK"><label class="chip-label" for="f5">卡拉OK</label>
                    <input type="checkbox" name="facility" id="f6" value="健身房"><label class="chip-label" for="f6">健身房</label>
                    <input type="checkbox" name="facility" id="f7" value="游泳池"><label class="chip-label" for="f7">游泳池</label>
                    <input type="checkbox" name="facility" id="f8" value="回收區"><label class="chip-label" for="f8">回收區</label>
                </div>
            </div>
            <div id="dynamic-container-facility"></div>
            <button type="button" class="btn-add-tool" onclick="addDynamicField('dynamic-container-facility', 'text')">➕ 新增公設備註</button>
        </div>

        <div class="card">
            <h2 class="card-title">產品特色</h2>
            <div class="form-group">
                <label>物件特色 (直接換行輸入)</label>
                <textarea id="features" rows="6" placeholder="產品特色&#10;位於棒球場豪宅特區的黃金地段...&#10;前院大面積綠化..."></textarea>
            </div>
            <div id="dynamic-container-features"></div>
            <button type="button" class="btn-add-tool" onclick="addDynamicField('dynamic-container-features', 'text')">➕ 往下新增特色備註</button>
        </div>

        <div class="card internal-card">
            <h2 class="card-title">內部機密資訊 (僅限複製純文字，不上圖)</h2>
            <div class="grid-2">
                <div class="form-group"><label for="intAddress">地址</label><input type="text" id="intAddress" placeholder="例：斗六市斗六五路19號十三樓之5"></div>
                <div class="form-group"><label for="intKey">鑰匙</label><input type="text" id="intKey" placeholder="自行填入"></div>
                <div class="form-group"><label for="intCondition">屋狀注意</label><input type="text" id="intCondition" placeholder="例：漏水"></div>
                <div class="form-group"><label for="intMemo">重要備註</label><input type="text" id="intMemo" placeholder="內部備註"></div>
            </div>
            <div id="dynamic-container-internal1"></div>
            <button type="button" class="btn-add-tool" onclick="addDynamicField('dynamic-container-internal1', 'text')" style="margin-bottom:15px;">➕ 新增內部備註</button>

            <div class="grid-2">
                <div class="form-group"><label for="intNo">物件編號</label><input type="text" id="intNo" placeholder="例：UA0392913"></div>
                <div class="form-group">
                    <label>委託種類</label>
                    <select id="intEntrust">
                        <option value="一般委託">一般委託</option>
                        <option value="專任委託">專任委託</option>
                    </select>
                </div>
                <div class="form-group"><label for="intDev">開發</label><input type="text" id="intDev" placeholder="例：小峰"></div>
                <div class="form-group"><label for="intFee">服務費</label><input type="text" id="intFee" placeholder="例：4%"></div>
                <div class="form-group">
                    <label>是否有中人費</label>
                    <select id="intMiddleman">
                        <option value="無">無</option>
                        <option value="有">有</option>
                    </select>
                </div>
            </div>
            <div id="dynamic-container-internal2"></div>
            <button type="button" class="btn-add-tool" onclick="addDynamicField('dynamic-container-internal2', 'text')">➕ 新增案件備註</button>
        </div>
    </form>
</div>

<div class="fixed-bottom-bar" id="bottomBar">
    <button type="button" class="submit-btn" id="generateBtn"><span>產生旗艦明細圖</span></button>
</div>

<div id="exportArea" class="export-wrapper">
    <div id="capture-area">
        <div class="preview-header">
            <div style="display:flex; align-items:center; gap:10px;">
                <div style="width: 30px; height: 30px; border-radius: 50%; background: var(--brand-yellow); border-bottom: 10px solid var(--brand-green); overflow: hidden;">
                    <div style="width: 100%; height: 10px; background: var(--brand-red); margin-top: 10px;"></div>
                </div>
                <span style="font-size: 24px; font-weight: 900; letter-spacing: 2px; color: var(--brand-dark);">永義房屋</span>
            </div>
            <div class="preview-type">[<span id="out-type-tag">大樓/公寓</span>]</div>
        </div>

        <div class="preview-title" id="out-title">案名</div>

        <div class="top-section">
            <div class="summary-col">
                <div class="data-item">
                    <div class="data-label">總價</div>
                    <div class="data-value"><span id="out-price">-</span><span>萬</span></div>
                </div>
                <div class="data-item">
                    <div class="data-label">建物登記</div>
                    <div class="data-value"><span id="out-ping">-</span><span>坪</span></div>
                </div>
                <div class="data-item">
                    <div class="data-label">格局</div>
                    <div class="data-value" id="out-layout" data-raw="-">-</div>
                </div>
            </div>
            <div class="photo-col">
                <img id="out-img-1" src="data:image/gif;base64,R0lGODlhAQABAIAAAP///wAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw==" alt="照片1">
                <img id="out-img-2" src="data:image/gif;base64,R0lGODlhAQABAIAAAP///wAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw==" alt="照片2">
            </div>
        </div>

        <div class="table-section">
            <div id="out-table-left">
                <div class="table-header">建築與空間</div>
                <div class="table-row"><div class="th">戶型</div><div class="td" id="out-unitType">-</div></div>
                <div class="table-row"><div class="th">樓層</div><div class="td" id="out-floor">-</div></div>
                <div class="table-row"><div class="th">車位詳情</div><div class="td" id="out-parkingDetails">-</div></div>
                <div class="table-row"><div class="th">車位坪數</div><div class="td" id="out-parkingArea">-</div></div>
                <div class="table-row"><div class="th">總戶數</div><div class="td" id="out-households">-</div></div>
                <div class="table-row"><div class="th">管理費</div><div class="td" id="out-manageFee">-</div></div>
                <div class="table-row"><div class="th">瓦斯來源</div><div class="td" id="out-gas">-</div></div>
                <div class="table-row"><div class="th">完成/屋齡</div><div class="td" id="out-age-date">-</div></div>
            </div>
            <div id="out-table-right">
                <div class="table-header">面積與法規</div>
                <div class="table-row"><div class="th">登記坪數</div><div class="td" id="out-ping-tbl">-坪</div></div>
                <div class="table-row"><div class="th">- 主建物</div><div class="td" id="out-mainArea">-</div></div>
                <div class="table-row"><div class="th">- 附屬建物</div><div class="td" id="out-subArea">-</div></div>
                <div class="table-row"><div class="th">- 公設</div><div class="td" id="out-publicArea">-</div></div>
                <div class="table-row"><div class="th">登記原因</div><div class="td" id="out-regReason">-</div></div>
                <div class="table-row"><div class="th">主要建材</div><div class="td" id="out-material">-</div></div>
                <div class="table-row"><div class="th">主要用途</div><div class="td" id="out-usage">-</div></div>
                <div class="table-row"><div class="th">使用分區</div><div class="td" id="out-zoning">-</div></div>
            </div>
        </div>

        <div class="features-section">
            <div class="table-header">公共設施</div>
            <div class="features-content" id="out-facility" style="color:#666; font-weight:normal; margin-bottom: 10px;">-</div>
            
            <div class="table-header" style="margin-top:15px;">物件特色</div>
            <div class="features-content" id="out-features"></div>
        </div>
    </div>
</div>

<script>
    // --- 類別選單行為控制 ---
    document.querySelectorAll('.cat-btn:not(.cat-disabled)').forEach(btn => {
        btn.addEventListener('click', function() {
            document.querySelectorAll('.cat-btn').forEach(b => b.classList.remove('active'));
            this.classList.add('active');
            document.getElementById('mainFormArea').style.display = 'block';
            document.getElementById('bottomBar').style.display = 'flex';
            
            const selectedType = this.getAttribute('data-type');
            document.getElementById('out-type-tag').innerText = selectedType;
            setTimeout(() => { document.getElementById('mainFormArea').scrollIntoView({ behavior: 'smooth' }); }, 100);
        });
    });

    // --- 日期下拉選單生成 ---
    const CURRENT_YEAR_ROC = 115; 
    const ySel = document.getElementById('buildYear');
    ySel.innerHTML = '<option value="">請選擇</option>';
    for(let i = CURRENT_YEAR_ROC; i >= 50; i--) ySel.innerHTML += `<option value="${i}">民國 ${i} 年</option>`;

    const mSel = document.getElementById('buildMonth');
    mSel.innerHTML = '<option value="">請選擇</option>';
    for(let i = 1; i <= 12; i++) mSel.innerHTML += `<option value="${i}">${i}</option>`;

    const dSel = document.getElementById('buildDay');
    dSel.innerHTML = '<option value="">請選擇</option>';
    for(let i = 1; i <= 31; i++) dSel.innerHTML += `<option value="${i}">${i}</option>`;

    // --- 其他選項切換邏輯 ---
    function toggleSelectOtherSimple(selectObj, otherInputId) {
        const otherInput = document.getElementById(otherInputId);
        if(selectObj.value === '其他') { otherInput.style.display = 'block'; otherInput.focus(); }
        else { otherInput.style.display = 'none'; }
    }

    document.querySelectorAll('input[name="gas"]').forEach(radio => {
        radio.addEventListener('change', function() {
            const otherInput = document.getElementById('gasOther');
            if (this.value === '其他') { otherInput.style.display = 'block'; otherInput.focus(); } 
            else { otherInput.style.display = 'none'; }
        });
    });

    // --- 照片預覽 ---
    function setupImageUpload(inputId, targetImgId) {
        document.getElementById(inputId).addEventListener('change', function(e) {
            if (e.target.files && e.target.files[0]) {
                const reader = new FileReader();
                reader.onload = function(evt) { document.getElementById(targetImgId).src = evt.target.result; }
                reader.readAsDataURL(e.target.files[0]);
            }
        });
    }
    setupImageUpload('uploadImg1', 'out-img-1');
    setupImageUpload('uploadImg2', 'out-img-2');

    // --- 千分位格式化 ---
    document.querySelectorAll('.format-num').forEach(input => {
        input.addEventListener('blur', function() {
            if(this.value) {
                let parts = this.value.replace(/,/g, '').split('.');
                parts[0] = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ",");
                this.value = parts.join('.');
            }
        });
        input.addEventListener('focus', function() { this.value = this.value.replace(/,/g, ''); });
    });

    // --- 動態新增工具 ---
    function addDynamicField(containerId, type) {
        const container = document.getElementById(containerId);
        const row = document.createElement('div');
        row.className = 'dynamic-row';
        if(type === 'table') {
            row.innerHTML = `<input type="text" class="dyn-key" placeholder="標題(如:採光)"><input type="text" class="dyn-val" placeholder="內容(如:極佳)"><button type="button" class="btn-remove-row" onclick="this.parentElement.remove()">×</button>`;
        } else {
            row.innerHTML = `<input type="text" class="dyn-val" placeholder="補充內容..."><button type="button" class="btn-remove-row" onclick="this.parentElement.remove()">×</button>`;
        }
        container.appendChild(row);
    }

    const getVal = (id) => document.getElementById(id).value.trim() || '-';
    const getPing = (id) => { const v = document.getElementById(id).value.trim(); return v ? v + '坪' : '-'; };

    // --- 產圖主邏輯 ---
    document.getElementById('generateBtn').addEventListener('click', function() {
        const btn = this;
        const btnText = btn.querySelector('span');
        const originalText = btnText.innerText;
        btnText.innerText = '圖片渲染中...';
        btn.disabled = true; btn.style.backgroundColor = '#001F5B';

        // 核心資料
        document.getElementById('out-title').innerText = getVal('caseName');
        document.getElementById('out-price').innerText = getVal('price');
        document.getElementById('out-ping').innerText = getVal('totalArea');
        document.getElementById('out-ping-tbl').innerText = getPing('totalArea');

        // 格局處理 (含紅色大字體特效)
        let lr = document.getElementById('layoutRoom').value.trim();
        let ll = document.getElementById('layoutLiving').value.trim();
        let lb = document.getElementById('layoutBath').value.trim();
        let lbal = document.getElementById('layoutBalcony').value.trim();
        let lo = document.getElementById('layoutOther').value.trim();
        
        let layoutRawStr = ''; 
        let layoutHtml = '';   
        const sNum = 'style="font-size: 38px; color: var(--brand-red); font-weight: 900;"';
        const sTxt = 'style="font-size: 18px; color: #333; font-weight: normal;"';

        if (lr || ll || lb || lbal) {
            let pRaw = [];
            let pHtml = [];
            if (lr) { pRaw.push(lr + '房'); pHtml.push(`<span ${sNum}>${lr}</span><span ${sTxt}>房</span>`); }
            if (ll) { pRaw.push(ll + '廳'); pHtml.push(`<span ${sNum}>${ll}</span><span ${sTxt}>廳</span>`); }
            if (lb) { pRaw.push(lb + '衛'); pHtml.push(`<span ${sNum}>${lb}</span><span ${sTxt}>衛</span>`); }
            if (lbal) { pRaw.push(lbal + '陽'); pHtml.push(`<span ${sNum}>${lbal}</span><span ${sTxt}>陽</span>`); }
            
            layoutRawStr = pRaw.join('');
            layoutHtml = pHtml.join('<span style="margin:0 2px;"></span>');
        } else if (lo) {
            layoutRawStr = lo;
            let safeLo = lo.replace(/</g, "&lt;").replace(/>/g, "&gt;"); 
            layoutHtml = `<span ${sTxt}>${safeLo.replace(/(\d+)/g, `<span ${sNum}>$1</span>`)}</span>`;
        } else {
            layoutRawStr = '-';
            layoutHtml = `<span ${sTxt}>-</span>`;
        }
        document.getElementById('out-layout').setAttribute('data-raw', layoutRawStr);
        document.getElementById('out-layout').innerHTML = layoutHtml;

        // 樓層處理
        let fa = document.getElementById('floorAt').value.trim();
        let ft = document.getElementById('floorTotal').value.trim();
        let fo = document.getElementById('floorOther').value.trim();
        let floorParts = [];
        if(fa || ft) floorParts.push(`位於${fa||'_'}樓/總共${ft||'_'}樓`);
        if(fo) floorParts.push(`(${fo})`);
        document.getElementById('out-floor').innerText = floorParts.length > 0 ? floorParts.join(' ') : '-';

        // 車位處理
        let pkChecked = document.querySelector('input[name="parkKind"]:checked');
        let pkVal = pkChecked ? pkChecked.value : '';
        let pa = document.getElementById('parkAt').value.trim();
        let pn = document.getElementById('parkNo').value.trim();
        let pt = document.getElementById('parkTotal').value.trim();
        let po = document.getElementById('parkOther').value.trim();

        let parkParts = [];
        if(pkVal) parkParts.push(pkVal);
        if(pa || pn || pt) parkParts.push(`位於地下${pa||'_'}層${pn||'_'}號/總共地下${pt||'_'}層`);
        if(po) parkParts.push(`(${po})`);
        document.getElementById('out-parkingDetails').innerText = parkParts.length > 0 ? parkParts.join(' ') : '-';

        // 建築雜項
        document.getElementById('out-unitType').innerText = getVal('unitType');
        document.getElementById('out-parkingArea').innerText = getPing('parkingArea');
        document.getElementById('out-households').innerText = getVal('totalHouseholds');
        document.getElementById('out-manageFee').innerText = getVal('manageFee');
        
        let gasChecked = document.querySelector('input[name="gas"]:checked');
        let gasVal = gasChecked ? gasChecked.value : '-';
        if (gasVal === '其他') gasVal = getVal('gasOther') === '-' ? '其他' : getVal('gasOther');
        document.getElementById('out-gas').innerText = gasVal;

        // 日期屋齡
        const age = document.getElementById('buildingAge').value.trim();
        const by = document.getElementById('buildYear').value;
        const bm = document.getElementById('buildMonth').value;
        const bd = document.getElementById('buildDay').value;
        let dateStr = '';
        if(by) dateStr += `${by}年`;
        if(bm) dateStr += `${bm}月`;
        if(bd) dateStr += `${bd}日`;
        
        let finalAgeStr = [];
        if(age) finalAgeStr.push(age + '年');
        if(dateStr) finalAgeStr.push(dateStr);
        document.getElementById('out-age-date').innerText = finalAgeStr.length > 0 ? finalAgeStr.join(' / ') : '-';

        // 動態左欄
        document.querySelectorAll('.out-dyn-row').forEach(e => e.remove());
        const bldRows = document.getElementById('dynamic-container-building').querySelectorAll('.dynamic-row');
        const leftTable = document.getElementById('out-table-left');
        bldRows.forEach(row => {
            const k = row.querySelector('.dyn-key').value.trim();
            const v = row.querySelector('.dyn-val').value.trim();
            if(k || v) {
                const tr = document.createElement('div');
                tr.className = 'table-row out-dyn-row';
                tr.innerHTML = `<div class="th" style="color:var(--brand-green);">${k||'備註'}</div><div class="td">${v||'-'}</div>`;
                leftTable.appendChild(tr);
            }
        });

        // 右欄法規
        document.getElementById('out-mainArea').innerText = getPing('mainArea');
        document.getElementById('out-subArea').innerText = getPing('subArea');
        document.getElementById('out-publicArea').innerText = getPing('publicArea');
        
        let regVal = document.getElementById('regReason').value;
        if(regVal === '其他') regVal = getVal('regReasonOther') === '-' ? '其他' : getVal('regReasonOther');
        document.getElementById('out-regReason').innerText = regVal;

        let matVal = document.getElementById('mainMaterial').value;
        if(matVal === '其他') matVal = getVal('materialOther') === '-' ? '其他' : getVal('materialOther');
        document.getElementById('out-material').innerText = matVal;

        document.getElementById('out-usage').innerText = getVal('mainUsage');

        let zoneVal = document.getElementById('zoning').value;
        if(zoneVal === '其他') zoneVal = getVal('zoningOther') === '-' ? '其他' : getVal('zoningOther');
        document.getElementById('out-zoning').innerText = zoneVal;

        // 公設
        let facArr = [];
        document.querySelectorAll('input[name="facility"]:checked').forEach(cb => facArr.push(cb.value));
        document.getElementById('dynamic-container-facility').querySelectorAll('.dyn-val').forEach(input => {
            if(input.value.trim()) facArr.push(input.value.trim());
        });
        document.getElementById('out-facility').innerText = facArr.length > 0 ? facArr.join('、') : '無';

        // 特色
        let featStr = document.getElementById('features').value;
        document.getElementById('dynamic-container-features').querySelectorAll('.dyn-val').forEach(input => {
            if(input.value.trim()) featStr += '\n- ' + input.value.trim(); 
        });
        document.getElementById('out-features').innerText = featStr;

        // 截圖
        const exportArea = document.getElementById('exportArea');
        exportArea.classList.add('capturing'); 

        setTimeout(() => {
            html2canvas(exportArea, { scale: 3, useCORS: true, backgroundColor: null }).then(canvas => {
                canvas.toBlob(function(blob) {
                    window.currentImageBlob = blob;
                    document.getElementById('previewImg').src = URL.createObjectURL(blob);
                    document.getElementById('previewModal').classList.add('show');
                    
                    exportArea.classList.remove('capturing');
                    btnText.innerText = originalText;
                    btn.disabled = false;
                    btn.style.backgroundColor = 'var(--brand-green)';
                }, 'image/png');
            });
        }, 300);
    });

    // --- Modal 行為 ---
    document.getElementById('backBtn').addEventListener('click', () => { document.getElementById('previewModal').classList.remove('show'); });

    document.getElementById('confirmDownloadBtn').addEventListener('click', function() {
        if (!window.currentImageBlob) return;
        const file = new File([window.currentImageBlob], '永義房屋_明細表.png', { type: 'image/png' });
        if (navigator.canShare && navigator.canShare({ files: [file] })) {
            navigator.share({ files: [file] }).catch(() => {});
        } else {
            const link = document.createElement('a');
            link.download = '永義房屋_明細表.png'; 
            link.href = URL.createObjectURL(window.currentImageBlob);
            document.body.appendChild(link); link.click(); link.remove();
        }
    });

    // --- ✨ 更新：極致詳細版 LINE 純文字複製 ---
    document.getElementById('copyLineBtn').addEventListener('click', function() {
        // 抓取各項詳細資訊
        const title = getVal('caseName');
        const price = getVal('price');
        const layout = document.getElementById('out-layout').getAttribute('data-raw');
        
        const totalArea = getVal('totalArea');
        const mainArea = getVal('mainArea');
        const subArea = getVal('subArea');
        const pubArea = getVal('publicArea');
        
        const parkDet = document.getElementById('out-parkingDetails').innerText; 
        const parkArea = getVal('parkingArea');

        const floor = document.getElementById('out-floor').innerText;
        const unitType = getVal('unitType');
        const households = getVal('totalHouseholds');
        const ageDate = document.getElementById('out-age-date').innerText;
        
        const manageFee = getVal('manageFee');
        const gas = document.getElementById('out-gas').innerText;
        const material = document.getElementById('out-material').innerText;
        const usage = getVal('mainUsage');
        const zoning = document.getElementById('out-zoning').innerText;
        const facility = document.getElementById('out-facility').innerText;

        // 蒐集動態特色
        let rawFeatures = document.getElementById('features').value;
        document.getElementById('dynamic-container-features').querySelectorAll('.dyn-val').forEach(inp => { if(inp.value.trim()) rawFeatures += `\n- ${inp.value.trim()}`; });

        // 蒐集內部備註
        let intNotes = '';
        document.getElementById('dynamic-container-internal1').querySelectorAll('.dyn-val').forEach(inp => { if(inp.value.trim()) intNotes += `\n- ${inp.value.trim()}`; });
        document.getElementById('dynamic-container-internal2').querySelectorAll('.dyn-val').forEach(inp => { if(inp.value.trim()) intNotes += `\n- ${inp.value.trim()}`; });

        // 組裝詳細文案
        const lineText = `【${title}】\n\n` +
                         `💰 總價：${price} 萬\n` +
                         `🛏️ 格局：${layout}\n` +
                         `🏢 樓層：${floor}\n\n` +
                         `【面積與車位】\n` +
                         `📐 權狀：${totalArea} 坪\n` +
                         (mainArea !== '-' ? `　🔸主建物：${mainArea} 坪\n` : '') +
                         (subArea !== '-' ? `　🔸附屬建物：${subArea} 坪\n` : '') +
                         (pubArea !== '-' ? `　🔸公設：${pubArea} 坪\n` : '') +
                         `🚗 車位：${parkDet} ${parkArea !== '-' ? '('+parkArea+'坪)' : ''}\n\n` +
                         `【建築與法規】\n` +
                         `⏳ 屋齡/完工：${ageDate}\n` +
                         `🏠 總戶數：${households} (戶型: ${unitType})\n` +
                         `💳 管理費：${manageFee}\n` +
                         `🔥 瓦斯：${gas}\n` +
                         `🧱 建材/用途：${material} / ${usage}\n` +
                         `📌 使用分區：${zoning}\n` +
                         `🏊 公共設施：${facility}\n\n` +
                         `【物件特色】\n${rawFeatures}\n\n` +
                         `====================\n` +
                         `【內部機密資訊】(請勿外流)\n` +
                         `📍 地址：${getVal('intAddress')}\n` +
                         `🔑 鑰匙：${getVal('intKey')}\n` +
                         `🛠️ 屋狀：${getVal('intCondition')}\n` +
                         `📝 備註：${getVal('intMemo')}${intNotes}\n` +
                         `📌 編號：${getVal('intNo')} (${document.getElementById('intEntrust').value})\n` +
                         `🤝 開發：${getVal('intDev')} / 服務費：${getVal('intFee')} / 中人費：${document.getElementById('intMiddleman').value}\n\n` +
                         `📞 永義房屋成功莊敬店\n公司電話：05-534-1000`;

        navigator.clipboard.writeText(lineText).then(() => {
            const btn = this;
            const originalText = btn.innerText;
            btn.innerText = "已複製詳細文案！";
            setTimeout(() => { btn.innerText = originalText; }, 3000);
        }).catch(err => {
            alert('複製失敗，請檢查瀏覽器權限。');
        });
    });
</script>

</body>
</html>
