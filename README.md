<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>ECOMASTA 盈利云 - 2025 高级利润计算器</title>
  <style>
    /* 基础深色主题 */
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: #121212;
      color: #e0e0e0;
      margin: 0; padding: 0;
    }
    header {
      background-color: #fb641b;
      color: #fff;
      padding: 1rem;
      text-align: center;
      font-size: 1.8rem;
      font-weight: 700;
      letter-spacing: 1px;
      user-select: none;
    }
    main {
      max-width: 900px;
      margin: 2rem auto;
      padding: 1rem 2rem;
      background: #1e1e1e;
      border-radius: 12px;
      box-shadow: 0 0 15px rgba(251, 100, 27, 0.4);
    }
    label {
      display: block;
      margin: 1rem 0 0.3rem 0;
      font-weight: 600;
    }
    select, input[type=number] {
      width: 100%;
      padding: 0.5rem;
      border-radius: 6px;
      border: none;
      font-size: 1rem;
      background: #2c2c2c;
      color: #eee;
      outline: none;
    }
    select:focus, input[type=number]:focus {
      box-shadow: 0 0 8px #fb641b;
    }
    .flex-row {
      display: flex;
      gap: 1rem;
      flex-wrap: wrap;
    }
    .flex-row > div {
      flex: 1 1 220px;
    }
    button {
      margin-top: 2rem;
      width: 100%;
      background-color: #fb641b;
      color: white;
      border: none;
      padding: 1rem;
      font-size: 1.2rem;
      border-radius: 8px;
      cursor: pointer;
      font-weight: 700;
      transition: background-color 0.3s ease;
    }
    button:hover {
      background-color: #e05200;
    }
    .results {
      margin-top: 2rem;
      background: #292929;
      padding: 1rem 1.5rem;
      border-radius: 10px;
      font-size: 1rem;
      line-height: 1.5;
      white-space: pre-line;
      user-select: text;
    }
    .small-note {
      font-size: 0.8rem;
      color: #aaa;
      margin-top: 0.2rem;
    }
  </style>
</head>
<body>
  <header>ECOMASTA 盈利云 - 2025 高级利润计算器</header>
  <main>
    <form id="calculatorForm">
      <div class="flex-row">
        <div>
          <label for="category">选择分类 (Category)</label>
          <select id="category" required>
            <option value="">--请选择分类--</option>
            <option value="mobile_accessories">手机配件</option>
            <option value="fashion">服装</option>
            <option value="beauty">美容</option>
            <option value="home_living">家居生活</option>
          </select>
        </div>
        <div>
          <label for="subcategory">选择子分类 (Sub-Category)</label>
          <select id="subcategory" required>
            <option value="">--请选择子分类--</option>
          </select>
        </div>
        <div>
          <label for="categoryCommission">类别佣金 (%)</label>
          <input type="number" id="categoryCommission" readonly />
        </div>
      </div>

      <div class="flex-row">
        <div>
          <label for="productPrice">卖价 (RM)</label>
          <input type="number" id="productPrice" min="0" step="0.01" required />
        </div>
        <div>
          <label for="cost">成本价 (RM)</label>
          <input type="number" id="cost" min="0" step="0.01" required />
        </div>
        <div>
          <label for="voucher">优惠券折扣 (RM)</label>
          <input type="number" id="voucher" min="0" step="0.01" value="0" />
        </div>
      </div>

      <div class="flex-row">
        <div>
          <label for="affiliate">联属佣金 (%)</label>
          <input type="number" id="affiliate" min="0" step="0.01" value="0" />
        </div>
        <div>
          <label for="serviceFee">服务费 (RM)</label>
          <input type="number" id="serviceFee" min="0" step="0.01" value="0" />
        </div>
        <div>
          <label for="influencerCommission">达人佣金 (%)</label>
          <input type="number" id="influencerCommission" min="0" step="0.01" value="0" />
          <div class="small-note">可选，如果没有就填0</div>
        </div>
      </div>

      <div class="flex-row">
        <div>
          <label for="targetProfit">目标利润 (RM)</label>
          <input type="number" id="targetProfit" min="0" step="0.01" value="0" />
          <div class="small-note">输入目标利润，系统反推建议售价</div>
        </div>
        <div>
          <label for="adBudget">广告预算总额 (RM)</label>
          <input type="number" id="adBudget" min="0" step="0.01" value="0" />
          <div class="small-note">输入多个产品时用于广告预算分配</div>
        </div>
      </div>

      <button type="button" onclick="calculate()">计算利润 & 广告建议</button>
    </form>

    <pre class="results" id="results"></pre>
  </main>

  <script>
    // 分类与子分类数据示例 + 类别佣金
    const categoryData = {
      mobile_accessories: {
        name: "手机配件",
        commission: 5.0,
        subcategories: {
          phone_case: { name: "手机壳", commission: 5.0 },
          data_cable: { name: "数据线", commission: 5.0 },
          charger: { name: "充电器", commission: 5.0 }
        }
      },
      fashion: {
        name: "服装",
        commission: 8.0,
        subcategories: {
          men_clothing: { name: "男装", commission: 8.0 },
          women_clothing: { name: "女装", commission: 8.0 },
          accessories: { name: "配饰", commission: 8.0 }
        }
      },
      beauty: {
        name: "美容",
        commission: 10.0,
        subcategories: {
          skincare: { name: "护肤", commission: 10.0 },
          makeup: { name: "彩妆", commission: 10.0 },
          haircare: { name: "美发", commission: 10.0 }
        }
      },
      home_living: {
        name: "家居生活",
        commission: 6.0,
        subcategories: {
          kitchen: { name: "厨房用品", commission: 6.0 },
          decor: { name: "家居装饰", commission: 6.0 },
          cleaning: { name: "清洁用品", commission: 6.0 }
        }
      }
    };

    // 填充子分类下拉菜单
    const categorySelect = document.getElementById("category");
    const subcategorySelect = document.getElementById("subcategory");
    const categoryCommissionInput = document.getElementById("categoryCommission");

    categorySelect.addEventListener("change", () => {
      const cat = categorySelect.value;
      subcategorySelect.innerHTML = '<option value="">--请选择子分类--</option>';
      categoryCommissionInput.value = "";
      if (!cat) return;

      const subcats = categoryData[cat].subcategories;
      for (const key in subcats) {
        const option = document.createElement("option");
        option.value = key;
        option.textContent = subcats[key].name;
        subcategorySelect.appendChild(option);
      }
      // 显示大类佣金（可选）
      categoryCommissionInput.value = categoryData[cat].commission;
    });

    // 选择子分类后自动更新佣金为子分类佣金
    subcategorySelect.addEventListener("change", () => {
      const cat = categorySelect.value;
      const subcat = subcategorySelect.value;
      if (!cat || !subcat) {
        categoryCommissionInput.value = "";
        return;
      }
      categoryCommissionInput.value = categoryData[cat].subcategories[subcat].commission;
    });

    function calculate() {
      const cat = categorySelect.value;
      const subcat = subcategorySelect.value;
      if (!cat || !subcat) {
        alert("请选择分类和子分类");
        return;
      }
      const categoryCommission = parseFloat(categoryCommissionInput.value) || 0;
      const productPrice = parseFloat(document.getElementById("productPrice").value) || 0;
      const cost = parseFloat(document.getElementById("cost").value) || 0;
      const voucher = parseFloat(document.getElementById("voucher").value) || 0;
      const affiliate = parseFloat(document.getElementById("affiliate").value) || 0;
      const serviceFee = parseFloat(document.getElementById("serviceFee").value) || 0;
      const influencerCommission = parseFloat(document.getElementById("influencerCommission").value) || 0;
      const targetProfit = parseFloat(document.getElementById("targetProfit").value) || 0;
      const adBudget = parseFloat(document.getElementById("adBudget").value) || 0;

      // 2025 最新平台交易手续费，固定3.78%
      const platformFeeRate = 0.0378;

      // 计算平台手续费：卖价 * 平台费率
      const platformFee = productPrice * platformFeeRate;

      // 计算联属佣金
      const affiliateFee = productPrice * (affiliate / 100);

      // 计算达人佣金
      const influencerFee = productPrice * (influencerCommission / 100);

      // 计算纯利润
      const profit = productPrice
        - cost
        - platformFee
        - affiliateFee
        - influencerFee
        - serviceFee
        - voucher
        - (productPrice * (categoryCommission / 100)); // 类别佣金

      // ROI 投资回报率
      const roi = cost > 0 ? (profit / cost) : 0;

      // 目标利润反推建议售价 = (目标利润 + 各项成本) / (1 - 总佣金率)
      // 总佣金率 = 类别佣金 + 平台手续费 + 联属佣金 + 达人佣金（都转成小数）
      const totalCommissionRate = (categoryCommission + platformFeeRate * 100 + affiliate + influencerCommission) / 100;
      const suggestedPrice = totalCommissionRate < 1
        ? (targetProfit + cost + serviceFee + voucher) / (1 - totalCommissionRate)
        : 0;

      // 简单输出计算结果
      let resultText = `产品类别：${categoryData[cat].name} > ${categoryData[cat].subcategories[subcat].name}\n`;
      resultText += `类别佣金：${categoryCommission.toFixed(2)}%\n`;
      resultText += `卖价：RM ${productPrice.toFixed(2)}\n`;
      resultText += `成本：RM ${cost.toFixed(2)}\n`;
      resultText += `优惠券折扣：RM ${voucher.toFixed(2)}\n`;
      resultText += `联属佣金：${affiliate.toFixed(2)}%\n`;
      resultText += `达人佣金：${influencerCommission.toFixed(2)}%\n`;
      resultText += `服务费：RM ${serviceFee.toFixed(2)}\n\n`;

      resultText += `平台手续费（3.78%）：RM ${platformFee.toFixed(2)}\n`;
      resultText += `纯利润：RM ${profit.toFixed(2)}\n`;
      resultText += `ROI（纯利/成本）：${(roi * 100).toFixed(2)}%\n\n`;

      if (targetProfit > 0) {
        resultText += `目标利润：RM ${targetProfit.toFixed(2)}\n`;
        resultText += `建议售价：RM ${suggestedPrice.toFixed(2)}\n\n`;
      }

      // 简单说明广告预算分配
      if (adBudget > 0) {
        // 假设只有当前一个产品，广告预算全部给它
        // 计算平均建议ROAS = 广告预算 / 纯利润，避免除0
        const avgROAS = profit > 0 ? (adBudget / profit) : 0;
        resultText += `广告预算总额: RM ${adBudget.toFixed(2)}\n`;
        resultText += `纯利润: RM ${profit.toFixed(2)}\n`;
        resultText += `整体平均建议 ROAS: ${avgROAS.toFixed(2)}\n`;
        resultText += `广告分配比例（利润占比为依据）:\n`;
        resultText += `产品 1: 建议广告预算 RM ${adBudget.toFixed(2)} （利润占比 100.00%）\n`;
      }

      document.getElementById("results").textContent = resultText;
    }
  </script>
</body>
</html>
