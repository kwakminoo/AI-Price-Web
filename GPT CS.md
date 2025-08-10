// File: src/TokenPriceApp.tsx
import React, { useMemo, useState } from "react";

// --- Minimal shadcn/ui-like primitives (fallbacks if not present) ---
const Button = ({ className = "", ...props }) => (
  <button
    className={
      "px-3 py-2 rounded-2xl shadow-sm border text-sm hover:shadow transition " +
      className
    }
    {...props}
  />
);
const Input = ({ className = "", ...props }) => (
  <input
    className={
      "px-3 py-2 rounded-xl border w-full outline-none focus:ring focus:ring-blue-200 " +
      className
    }
    {...props}
  />
);
const Select = ({ className = "", children, ...props }) => (
  <select
    className={
      "px-3 py-2 rounded-xl border w-full outline-none focus:ring focus:ring-blue-200 bg-white " +
      className
    }
    {...props}
  >
    {children}
  </select>
);
const Checkbox = ({ className = "", ...props }) => (
  <input type="checkbox" className={"w-4 h-4 " + className} {...props} />
);
const Card = ({ className = "", ...props }) => (
  <div className={"rounded-2xl border shadow-sm bg-white " + className} {...props} />
);
const CardHeader = ({ className = "", ...props }) => (
  <div className={"p-4 border-b bg-gray-50 rounded-t-2xl " + className} {...props} />
);
const CardContent = ({ className = "", ...props }) => (
  <div className={"p-4 " + className} {...props} />
);

// --- Tiny tooltip & info dot ---
const InfoDot = ({ title }: { title: string }) => (
  <span
    className="group ml-1 inline-flex h-4 w-4 items-center justify-center rounded-full border text-[10px] text-gray-600 cursor-help"
    title={title}
    aria-label={title}
  >
    ?
  </span>
);

// --- i18n strings ---
export const STR = {
  ko: {
    title: "AI 토큰 가격 비교",
    subtitle:
      "pricepertoken 스타일의 단일 화면. 카테고리 선택 → 컬럼 토글 → 가격 비교/계산.",
    categories: { chat: "대화형", code: "코딩", image: "이미지", video: "영상", other: "기타" },
    ui: {
      model: "모델",
      vendor: "벤더",
      view: "보기",
      monthly: "월간",
      yearly: "연간",
      token: "토큰(1K 기준)",
      baseMo: "기본료 (USD/월)",
      baseYr: "기본료 (USD/년)",
      included: "포함량 (K/월)",
      overIn: "초과 Input (USD/1K)",
      overOut: "초과 Output (USD/1K)",
      estUsdMo: "예상 총액 (USD/월)",
      estUsdYr: "예상 총액 (USD/년)",
      estKrwMo: "예상 총액 (KRW/월)",
      estKrwYr: "예상 총액 (KRW/년)",
      sync: "가격 동기화",
      assumedMonthly: "가정 월 사용량 (K 토큰)",
      items: "개 항목",
      billing: "청구 주기",
      yes: "예",
      no: "아니오",
      inputKPerMo: "월 Input 토큰(K)",
      outputKPerMo: "월 Output 토큰(K)",
      tokenView: "토큰(1K)",
      monthlyView: "월간",
      yearlyView: "연간",
      official: "공식 페이지",
    },
    columns: {
      input: "Input(1K / USD)",
      output: "Output(1K / USD)",
      krw: "원화 환산(1K)",
      context: "컨텍스트",
      bench: "벤치마크/성능",
      latency: "지연시간",
      langs: "언어",
      api: "API",
      launched: "출시일",
      alt: "오픈소스 대체",
    },
    help: {
      base: "구독 기본료입니다. 월/연 단위로 과금되는 고정 비용.",
      included: "구독에 포함된 월간 무료 토큰(입력/출력)입니다.",
      overIn: "포함량 초과 시 입력 토큰 1K당 추가 요금.",
      overOut: "포함량 초과 시 출력 토큰 1K당 추가 요금.",
      est: "가정한 월 사용량 기준으로 계산된 예상 총 비용입니다. 실제 과금은 벤더 정책에 따라 다를 수 있습니다.",
      tokenCols: "토큰 단가(1K 기준) 비교용 기본 보기입니다.",
    },
    toggleCols: "표시할 컬럼 선택",
    exchange: "환율(₩/USD)",
    search: "모델 검색",
    calcTitle: "비용 계산기",
    calcModel: "모델 선택",
    calcIn: "월 Input 토큰(K)",
    calcOut: "월 Output 토큰(K)",
    calcBtn: "계산",
    usd: "달러",
    krw: "원",
    perMonth: "/월",
    empty: "해당 조건의 데이터가 없습니다.",
    referral: "공식 페이지",
  },
  en: {
    title: "AI Token Price Comparator",
    subtitle:
      "Single-screen like pricepertoken. Pick a category, toggle columns, compare/calc.",
    categories: { chat: "Chat", code: "Code", image: "Image", video: "Video", other: "Other" },
    ui: {
      model: "Model",
      vendor: "Vendor",
      view: "View",
      monthly: "Monthly",
      yearly: "Yearly",
      token: "Token (per 1K)",
      baseMo: "Base (USD/mo)",
      baseYr: "Base (USD/yr)",
      included: "Included (K/mo)",
      overIn: "Overage In (USD/1K)",
      overOut: "Overage Out (USD/1K)",
      estUsdMo: "Est. Total (USD/mo)",
      estUsdYr: "Est. Total (USD/yr)",
      estKrwMo: "Est. Total (KRW/mo)",
      estKrwYr: "Est. Total (KRW/yr)",
      sync: "Sync Prices",
      assumedMonthly: "Assumed monthly usage (K tokens)",
      items: "items",
      billing: "Billing",
      yes: "Yes",
      no: "No",
      inputKPerMo: "Input K / mo",
      outputKPerMo: "Output K / mo",
      tokenView: "Token (per 1K)",
      monthlyView: "Monthly",
      yearlyView: "Yearly",
      official: "Official",
    },
    columns: {
      input: "Input(1K / USD)",
      output: "Output(1K / USD)",
      krw: "KRW(1K)",
      context: "Context",
      bench: "Benchmark",
      latency: "Latency",
      langs: "Langs",
      api: "API",
      launched: "Launched",
      alt: "Open-source Alt",
    },
    help: {
      base: "Subscription base fee billed monthly/yearly.",
      included: "Monthly free tokens (input/output) included in the plan.",
      overIn: "Additional fee per 1K input tokens beyond included quota.",
      overOut: "Additional fee per 1K output tokens beyond included quota.",
      est: "Estimated total cost using your assumed monthly usage. Actual billing may vary.",
      tokenCols: "Default token-per-1K comparison view.",
    },
    toggleCols: "Toggle columns",
    exchange: "FX(₩/USD)",
    search: "Search model",
    calcTitle: "Cost Calculator",
    calcModel: "Model",
    calcIn: "Monthly Input Tokens(K)",
    calcOut: "Monthly Output Tokens(K)",
    calcBtn: "Calculate",
    usd: "USD",
    krw: "KRW",
    perMonth: "/mo",
    empty: "No data matches your filters.",
    referral: "Official",
  },
  ja: {
    title: "AIトークン価格比較",
    subtitle: "pricepertoken風の単一画面。カテゴリ選択→列切替→比較/計算。",
    categories: { chat: "チャット", code: "コーディング", image: "画像", video: "動画", other: "その他" },
    ui: {
      model: "モデル",
      vendor: "ベンダー",
      view: "表示",
      monthly: "月間",
      yearly: "年間",
      token: "トークン(1K基準)",
      baseMo: "基本料 (USD/月)",
      baseYr: "基本料 (USD/年)",
      included: "含有量 (K/月)",
      overIn: "超過 Input (USD/1K)",
      overOut: "超過 Output (USD/1K)",
      estUsdMo: "推定合計 (USD/月)",
      estUsdYr: "推定合計 (USD/年)",
      estKrwMo: "推定合計 (KRW/月)",
      estKrwYr: "推定合計 (KRW/年)",
      sync: "価格同期",
      assumedMonthly: "想定月間使用量 (Kトークン)",
      items: "件",
      billing: "課金周期",
      yes: "はい",
      no: "いいえ",
      inputKPerMo: "月間入力トークン(K)",
      outputKPerMo: "月間出力トークン(K)",
      tokenView: "トークン(1K)",
      monthlyView: "月額",
      yearlyView: "年額",
      official: "公式",
    },
    columns: {
      input: "入力(1K / USD)",
      output: "出力(1K / USD)",
      krw: "KRW(1K)",
      context: "コンテキスト",
      bench: "ベンチマーク",
      latency: "レイテンシ",
      langs: "言語",
      api: "API",
      launched: "リリース",
      alt: "OSS代替",
    },
    help: {
      base: "サブスクリプションの基本料金。月/年で請求。",
      included: "プランに含まれる月間無料トークン(入力/出力)。",
      overIn: "含有量超過時の入力1Kトークン追加料金。",
      overOut: "含有量超過時の出力1Kトークン追加料金。",
      est: "想定使用量に基づく推定総額。実際の請求はベンダーで異なる場合あり。",
      tokenCols: "1Kあたりのトークン単価比較のデフォルト表示。",
    },
    toggleCols: "列の表示切替",
    exchange: "為替(₩/USD)",
    search: "モデル検索",
    calcTitle: "コスト計算",
    calcModel: "モデル",
    calcIn: "月間入力トークン(K)",
    calcOut: "月間出力トークン(K)",
    calcBtn: "計算",
    usd: "USD",
    krw: "KRW",
    perMonth: "/月",
    empty: "条件に一致するデータがありません。",
    referral: "公式",
  },
  zh: {
    title: "AI 代币价格对比",
    subtitle: "pricepertoken 风格：选择分类→切换列→对比/计算。",
    categories: { chat: "对话", code: "编码", image: "图像", video: "视频", other: "其他" },
    ui: {
      model: "模型",
      vendor: "厂商",
      view: "视图",
      monthly: "月度",
      yearly: "年度",
      token: "代币(每1K)",
      baseMo: "基础费 (USD/月)",
      baseYr: "基础费 (USD/年)",
      included: "包含量 (K/月)",
      overIn: "超额输入 (USD/1K)",
      overOut: "超额输出 (USD/1K)",
      estUsdMo: "预计总额 (USD/月)",
      estUsdYr: "预计总额 (USD/年)",
      estKrwMo: "预计总额 (KRW/月)",
      estKrwYr: "预计总额 (KRW/年)",
      sync: "同步价格",
      assumedMonthly: "假设月度用量 (K代币)",
      items: "项",
      billing: "计费周期",
      yes: "是",
      no: "否",
      inputKPerMo: "每月输入(K)",
      outputKPerMo: "每月输出(K)",
      tokenView: "代币(每1K)",
      monthlyView: "月度",
      yearlyView: "年度",
      official: "官网",
    },
    columns: {
      input: "输入(1K / USD)",
      output: "输出(1K / USD)",
      krw: "韩元(1K)",
      context: "上下文",
      bench: "基准",
      latency: "延迟",
      langs: "语言",
      api: "API",
      launched: "发布",
      alt: "开源替代",
    },
    help: {
      base: "订阅基础费用，按月/年计费。",
      included: "订阅包含的每月免费代币(输入/输出)。",
      overIn: "超出包含量时每1K输入代币的费用。",
      overOut: "超出包含量时每1K输出代币的费用。",
      est: "基于假设用量的预计总费用，实际账单可能不同。",
      tokenCols: "默认的每1K代币单价对比视图。",
    },
    toggleCols: "切换列",
    exchange: "汇率(₩/USD)",
    search: "搜索模型",
    calcTitle: "费用计算器",
    calcModel: "模型",
    calcIn: "每月输入代币(K)",
    calcOut: "每月输出代币(K)",
    calcBtn: "计算",
    usd: "USD",
    krw: "KRW",
    perMonth: "/月",
    empty: "没有符合条件的数据。",
    referral: "官网",
  },
} as const;

// --- Data (placeholders; replace with real pricing) ---
type Model = {
  name: string;
  vendor: string;
  category: "chat" | "code" | "image" | "video" | "other";
  inputUSDPerK: number;
  outputUSDPerK: number;
  context: string;
  benchmark: string;
  latency: string;
  languages: string[];
  api: boolean;
  launched: string;
  alt: string;
  official: string;
  monthlyBaseUSD: number;
  yearlyBaseUSD: number;
  freeInKPerMonth: number;
  freeOutKPerMonth: number;
  overageInputUSDPerK: number;
  overageOutputUSDPerK: number;
};

export const MODEL_DATA: Model[] = [
  // Chat
  {
    name: "OpenAI GPT-4o",
    vendor: "OpenAI",
    category: "chat",
    inputUSDPerK: 0.005,
    outputUSDPerK: 0.015,
    context: "128K",
    benchmark: "MMLU ~88% (ref)",
    latency: "Low",
    languages: ["ko", "en", "ja", "zh"],
    api: true,
    launched: "2024-05",
    alt: "Llama 3.1 70B",
    official: "https://platform.openai.com/pricing",
    monthlyBaseUSD: 0,
    yearlyBaseUSD: 0,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0.005,
    overageOutputUSDPerK: 0.015,
  },
  {
    name: "Anthropic Claude 3.5 Sonnet",
    vendor: "Anthropic",
    category: "chat",
    inputUSDPerK: 0.008,
    outputUSDPerK: 0.024,
    context: "200K",
    benchmark: "MMLU ~86% (ref)",
    latency: "Low",
    languages: ["ko", "en", "ja"],
    api: true,
    launched: "2024-06",
    alt: "Llama 3.1 70B",
    official: "https://www.anthropic.com/pricing",
    monthlyBaseUSD: 0,
    yearlyBaseUSD: 0,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0.008,
    overageOutputUSDPerK: 0.024,
  },
  {
    name: "Google Gemini 1.5 Pro",
    vendor: "Google",
    category: "chat",
    inputUSDPerK: 0.007,
    outputUSDPerK: 0.021,
    context: "128K",
    benchmark: "MMLU ~85% (ref)",
    latency: "Med",
    languages: ["ko", "en", "ja", "zh"],
    api: true,
    launched: "2024-02",
    alt: "Llama 3.1 70B",
    official: "https://ai.google.dev/pricing",
    monthlyBaseUSD: 0,
    yearlyBaseUSD: 0,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0.007,
    overageOutputUSDPerK: 0.021,
  },
  {
    name: "NAVER HyperCLOVA X",
    vendor: "NAVER",
    category: "chat",
    inputUSDPerK: 0.006,
    outputUSDPerK: 0.018,
    context: "?",
    benchmark: "KR Bench (ref)",
    latency: "Med",
    languages: ["ko"],
    api: true,
    launched: "2024-09",
    alt: "Llama 3.1 8B ko",
    official: "https://clova-x.naver.com/",
    monthlyBaseUSD: 0,
    yearlyBaseUSD: 0,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0.006,
    overageOutputUSDPerK: 0.018,
  },
  // Code
  {
    name: "OpenAI o3-mini (code)",
    vendor: "OpenAI",
    category: "code",
    inputUSDPerK: 0.003,
    outputUSDPerK: 0.009,
    context: "128K",
    benchmark: "HumanEval (ref)",
    latency: "Low",
    languages: ["ko", "en", "ja", "zh"],
    api: true,
    launched: "2024-12",
    alt: "StarCoder2",
    official: "https://platform.openai.com/pricing",
    monthlyBaseUSD: 0,
    yearlyBaseUSD: 0,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0.003,
    overageOutputUSDPerK: 0.009,
  },
  {
    name: "Tabnine",
    vendor: "Tabnine",
    category: "code",
    inputUSDPerK: 0.0,
    outputUSDPerK: 0.0,
    context: "-",
    benchmark: "IDE Assistant",
    latency: "Local/Cloud",
    languages: ["en", "ja"],
    api: false,
    launched: "-",
    alt: "Code Llama",
    official: "https://www.tabnine.com/pricing",
    monthlyBaseUSD: 12,
    yearlyBaseUSD: 120,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0,
    overageOutputUSDPerK: 0,
  },
  // Image
  {
    name: "OpenAI DALL·E 3",
    vendor: "OpenAI",
    category: "image",
    inputUSDPerK: 0.0,
    outputUSDPerK: 0.04,
    context: "prompt-based",
    benchmark: "Aesthetic (ref)",
    latency: "Med",
    languages: ["ko", "en", "ja", "zh"],
    api: true,
    launched: "2023-10",
    alt: "SDXL",
    official: "https://platform.openai.com/pricing",
    monthlyBaseUSD: 0,
    yearlyBaseUSD: 0,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0,
    overageOutputUSDPerK: 0.04,
  },
  {
    name: "Midjourney v6",
    vendor: "Midjourney",
    category: "image",
    inputUSDPerK: 0.0,
    outputUSDPerK: 0.0,
    context: "subscription",
    benchmark: "Aesthetic (ref)",
    latency: "Med",
    languages: ["en", "ja"],
    api: false,
    launched: "2023-12",
    alt: "SDXL",
    official: "https://www.midjourney.com/home",
    monthlyBaseUSD: 10,
    yearlyBaseUSD: 96,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0,
    overageOutputUSDPerK: 0,
  },
  {
    name: "Stable Diffusion XL",
    vendor: "Stability",
    category: "image",
    inputUSDPerK: 0.0,
    outputUSDPerK: 0.0,
    context: "open-source",
    benchmark: "-",
    latency: "Varies",
    languages: ["-"],
    api: true,
    launched: "2023-07",
    alt: "SD 1.5",
    official: "https://platform.stability.ai/",
    monthlyBaseUSD: 0,
    yearlyBaseUSD: 0,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0,
    overageOutputUSDPerK: 0,
  },
  // Video
  {
    name: "Runway Gen-3",
    vendor: "Runway",
    category: "video",
    inputUSDPerK: 0.0,
    outputUSDPerK: 0.0,
    context: "credits",
    benchmark: "-",
    latency: "Med",
    languages: ["en", "ja"],
    api: true,
    launched: "2024-06",
    alt: "Pika",
    official: "https://runwayml.com/pricing",
    monthlyBaseUSD: 12,
    yearlyBaseUSD: 120,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0,
    overageOutputUSDPerK: 0,
  },
  {
    name: "Pika 2",
    vendor: "Pika Labs",
    category: "video",
    inputUSDPerK: 0.0,
    outputUSDPerK: 0.0,
    context: "credits",
    benchmark: "-",
    latency: "Med",
    languages: ["en", "ja", "zh"],
    api: true,
    launched: "2024-01",
    alt: "Runway",
    official: "https://pika.art/",
    monthlyBaseUSD: 10,
    yearlyBaseUSD: 96,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0,
    overageOutputUSDPerK: 0,
  },
  // Other (speech, tts)
  {
    name: "OpenAI Whisper (STT)",
    vendor: "OpenAI",
    category: "other",
    inputUSDPerK: 0.006,
    outputUSDPerK: 0.0,
    context: "audio min",
    benchmark: "WER (ref)",
    latency: "Med",
    languages: ["ko", "en", "ja", "zh"],
    api: true,
    launched: "2023-03",
    alt: "Vosk",
    official: "https://platform.openai.com/pricing",
    monthlyBaseUSD: 0,
    yearlyBaseUSD: 0,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0.006,
    overageOutputUSDPerK: 0,
  },
  {
    name: "ElevenLabs TTS",
    vendor: "ElevenLabs",
    category: "other",
    inputUSDPerK: 0.0,
    outputUSDPerK: 0.0,
    context: "chars",
    benchmark: "MOS (ref)",
    latency: "Low",
    languages: ["ko", "en", "ja", "zh"],
    api: true,
    launched: "2023-06",
    alt: "XTTS",
    official: "https://elevenlabs.io/pricing",
    monthlyBaseUSD: 5,
    yearlyBaseUSD: 48,
    freeInKPerMonth: 0,
    freeOutKPerMonth: 0,
    overageInputUSDPerK: 0,
    overageOutputUSDPerK: 0,
  },
];

const CATEGORIES = [
  { id: "chat", key: "chat", color: "bg-blue-600" },
  { id: "code", key: "code", color: "bg-emerald-600" },
  { id: "image", key: "image", color: "bg-pink-600" },
  { id: "video", key: "video", color: "bg-purple-600" },
  { id: "other", key: "other", color: "bg-gray-600" },
];

const TOGGLABLE_COLS = ["context", "bench", "latency", "langs", "api", "launched", "alt"];

export default function TokenPriceApp() {
  const [lang, setLang] = useState<keyof typeof STR>("ko");
  const T = STR[lang];
  const [activeCat, setActiveCat] = useState("chat");
  const [fx, setFx] = useState(1400); // KRW per USD (editable)
  const [query, setQuery] = useState("");
  const [view, setView] = useState<"token" | "monthly" | "yearly">("token");

  const [cols, setCols] = useState<Set<string>>(() => new Set(TOGGLABLE_COLS));
  const toggleCol = (k: string) => {
    setCols((prev) => {
      const next = new Set(prev);
      if (next.has(k)) next.delete(k);
      else next.add(k);
      return next;
    });
  };

  const data = useMemo(() => {
    return MODEL_DATA.filter((m) => m.category === activeCat).filter((m) =>
      query.trim()
        ? (m.name + " " + m.vendor).toLowerCase().includes(query.toLowerCase())
        : true
    );
  }, [activeCat, query]);

  // Calculator & assumed usage (monthly)
  const [calcModelName, setCalcModelName] = useState("");
  const [inK, setInK] = useState(0); // monthly input tokens (K)
  const [outK, setOutK] = useState(0); // monthly output tokens (K)
  const calcModel = useMemo(
    () => MODEL_DATA.find((m) => m.name === calcModelName),
    [calcModelName]
  );

  async function fetchOfficialPrice(model: Model) {
    // Placeholder: 실제 환경에서는 Next.js API 라우트(`/api/scrape`)에서 갱신
    return {
      monthlyBaseUSD: model.monthlyBaseUSD,
      yearlyBaseUSD: model.yearlyBaseUSD,
      freeInKPerMonth: model.freeInKPerMonth,
      freeOutKPerMonth: model.freeOutKPerMonth,
      overageInputUSDPerK: model.overageInputUSDPerK ?? model.inputUSDPerK,
      overageOutputUSDPerK: model.overageOutputUSDPerK ?? model.outputUSDPerK,
    };
  }

  function computeTokenCostUSD(model: Model, inKVal: number, outKVal: number) {
    const input = inKVal * (model.inputUSDPerK || 0);
    const output = outKVal * (model.outputUSDPerK || 0);
    return input + output;
  }

  function computePlanCostUSD(
    model: Model,
    inKMonthly: number,
    outKMonthly: number,
    period: "monthly" | "yearly" = "monthly"
  ) {
    const mult = period === "yearly" ? 12 : 1;
    const freeIn = model.freeInKPerMonth || 0;
    const freeOut = model.freeOutKPerMonth || 0;
    const overIn = Math.max(0, inKMonthly - freeIn);
    const overOut = Math.max(0, outKMonthly - freeOut);
    const overUSD =
      overIn * (model.overageInputUSDPerK ?? model.inputUSDPerK ?? 0) +
      overOut * (model.overageOutputUSDPerK ?? model.outputUSDPerK ?? 0);
    const baseUSD = period === "yearly" ? (model.yearlyBaseUSD || 0) : (model.monthlyBaseUSD || 0);
    return baseUSD + overUSD * mult;
  }

  const { costUSD, costKRW } = useMemo(() => {
    if (!calcModel) return { costUSD: 0, costKRW: 0 };
    if (view === "token") {
      const usd = computeTokenCostUSD(calcModel, inK, outK);
      return { costUSD: usd, costKRW: Math.round(usd * fx) };
    }
    const usd = computePlanCostUSD(calcModel, inK, outK, view);
    return { costUSD: usd, costKRW: Math.round(usd * fx) };
  }, [calcModel, inK, outK, fx, view]);

  const ColHeader = ({ label }: { label: React.ReactNode }) => (
    <th className="text-left text-xs md:text-sm font-medium text-gray-600 py-2 px-3 whitespace-nowrap">
      {label}
    </th>
  );
  const Cell = ({ children }: { children: React.ReactNode }) => (
    <td className="py-2 px-3 text-sm text-gray-900 whitespace-nowrap">{children}</td>
  );

  return (
    <div className="min-h-screen w-full bg-gradient-to-b from-gray-50 to-white text-gray-900">
      <div className="max-w-6xl mx-auto p-4 md:p-6">
        {/* Header */}
        <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-3 mb-4">
          <div>
            <h1 className="text-2xl md:text-3xl font-bold tracking-tight">{T.title}</h1>
            <p className="text-sm text-gray-600 mt-1">{T.subtitle}</p>
          </div>
          <div className="flex items-center gap-2">
            <Select value={lang} onChange={(e) => setLang(e.target.value as any)} className="w-[120px]">
              <option value="ko">한국어</option>
              <option value="en">English</option>
              <option value="ja">日本語</option>
              <option value="zh">中文</option>
            </Select>
            <div className="flex items-center gap-2">
              <span className="text-sm text-gray-600">{T.exchange}</span>
              <Input
                type="number"
                value={fx}
                onChange={(e) => setFx(Number(e.target.value) || 0)}
                className="w-28"
              />
            </div>
          </div>
        </div>

        {/* Category Tabs */}
        <div className="flex flex-wrap gap-2 mb-4">
          {CATEGORIES.map((c) => (
            <Button
              key={c.id}
              onClick={() => setActiveCat(c.id)}
              className={
                (activeCat === c.id ? c.color + " text-white " : "bg-white ") +
                " border px-4 py-2 rounded-2xl"
              }
            >
              {STR[lang].categories[c.key]}
            </Button>
          ))}
          <div className="ml-auto flex items-center gap-2">
            <span className="text-xs text-gray-600">{STR[lang].ui.view}</span>
            <Select value={view} onChange={(e) => setView(e.target.value as any)} className="w-[140px]">
              <option value="token">{STR[lang].ui.tokenView}</option>
              <option value="monthly">{STR[lang].ui.monthlyView}</option>
              <option value="yearly">{STR[lang].ui.yearlyView}</option>
            </Select>
            <Button
              className="bg-white"
              onClick={() =>
                alert("연동 팁: Next.js API 라우트(/api/scrape)로 공식 가격을 주기적 동기화하세요.")
              }
            >
              {STR[lang].ui.sync}
            </Button>
          </div>
        </div>

        {/* Toolbar: search & toggles / assumed usage */}
        <Card className="mb-4">
          <CardContent>
            <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-3">
              <div className="w-full md:w-1/2">
                <label className="text-xs text-gray-600">{T.search}</label>
                <Input
                  placeholder="GPT, Claude, Llama..."
                  value={query}
                  onChange={(e) => setQuery(e.target.value)}
                />
              </div>
              {view === "token" ? (
                <div className="w-full md:w-1/2">
                  <span className="block text-xs text-gray-600 mb-1">{T.toggleCols}</span>
                  <div className="grid grid-cols-2 md:grid-cols-4 gap-2">
                    {TOGGLABLE_COLS.map((k) => (
                      <label key={k} className="flex items-center gap-2 text-sm">
                        <Checkbox checked={cols.has(k)} onChange={() => toggleCol(k)} />
                        <span>{T.columns[k as keyof typeof T.columns]}</span>
                      </label>
                    ))}
                  </div>
                </div>
              ) : (
                <div className="w-full md:w-1/2">
                  <span className="block text-xs text-gray-600 mb-1">{T.ui.assumedMonthly}</span>
                  <div className="grid grid-cols-2 gap-2">
                    <div>
                      <label className="text-[11px] text-gray-500">{T.ui.inputKPerMo}</label>
                      <Input
                        type="number"
                        value={inK}
                        onChange={(e) => setInK(Number(e.target.value) || 0)}
                      />
                    </div>
                    <div>
                      <label className="text-[11px] text-gray-500">{T.ui.outputKPerMo}</label>
                      <Input
                        type="number"
                        value={outK}
                        onChange={(e) => setOutK(Number(e.target.value) || 0)}
                      />
                    </div>
                  </div>
                </div>
              )}
            </div>
          </CardContent>
        </Card>

        {/* Table */}
        <Card>
          <CardHeader>
            <div className="flex items-center justify-between">
              <span className="text-sm font-medium text-gray-700">
                {STR[lang].categories[activeCat as keyof typeof STR["ko"]["categories"]]}
              </span>
              <span className="text-xs text-gray-500">
                {data.length} {STR[lang].ui.items}
              </span>
            </div>
          </CardHeader>
          <CardContent className="overflow-x-auto">
            {view === "token" ? (
              <table className="min-w-full">
                <thead>
                  <tr>
                    <ColHeader label={STR[lang].ui.model} />
                    <ColHeader label={STR[lang].ui.vendor} />
                    <ColHeader
                      label={
                        <span>
                          {T.columns.input}
                          <InfoDot title={T.help.tokenCols} />
                        </span>
                      }
                    />
                    <ColHeader
                      label={
                        <span>
                          {T.columns.output}
                          <InfoDot title={T.help.tokenCols} />
                        </span>
                      }
                    />
                    <ColHeader
                      label={
                        <span>
                          {T.columns.krw}
                          <InfoDot title={T.help.tokenCols} />
                        </span>
                      }
                    />
                    {cols.has("context") && <ColHeader label={T.columns.context} />}
                    {cols.has("bench") && <ColHeader label={T.columns.bench} />}
                    {cols.has("latency") && <ColHeader label={T.columns.latency} />}
                    {cols.has("langs") && <ColHeader label={T.columns.langs} />}
                    {cols.has("api") && <ColHeader label={T.columns.api} />}
                    {cols.has("launched") && <ColHeader label={T.columns.launched} />}
                    {cols.has("alt") && <ColHeader label={T.columns.alt} />}
                    <ColHeader label={T.referral} />
                  </tr>
                </thead>
                <tbody>
                  {data.length === 0 && (
                    <tr>
                      <td className="py-6 text-center text-sm text-gray-500" colSpan={12}>
                        {T.empty}
                      </td>
                    </tr>
                  )}
                  {data.map((m) => {
                    const krwIn = Math.round((m.inputUSDPerK || 0) * fx);
                    const krwOut = Math.round((m.outputUSDPerK || 0) * fx);
                    const krwRange = `${krwIn.toLocaleString()} / ${krwOut.toLocaleString()} ${T.krw}`;
                    return (
                      <tr key={m.name} className="border-t">
                        <Cell>{m.name}</Cell>
                        <Cell>{m.vendor}</Cell>
                        <Cell>{(m.inputUSDPerK || 0).toFixed(3)}</Cell>
                        <Cell>{(m.outputUSDPerK || 0).toFixed(3)}</Cell>
                        <Cell>{krwRange}</Cell>
                        {cols.has("context") && <Cell>{m.context}</Cell>}
                        {cols.has("bench") && <Cell>{m.benchmark}</Cell>}
                        {cols.has("latency") && <Cell>{m.latency}</Cell>}
                        {cols.has("langs") && <Cell>{m.languages?.join(", ")}</Cell>}
                        {cols.has("api") && <Cell>{m.api ? T.ui.yes : T.ui.no}</Cell>}
                        {cols.has("launched") && <Cell>{m.launched}</Cell>}
                        {cols.has("alt") && <Cell>{m.alt}</Cell>}
                        <Cell>
                          <a
                            href={m.official}
                            target="_blank"
                            rel="noreferrer"
                            className="text-blue-600 underline"
                          >
                            {T.referral}
                          </a>
                        </Cell>
                      </tr>
                    );
                  })}
                </tbody>
              </table>
            ) : (
              <table className="min-w-full">
                <thead>
                  <tr>
                    <ColHeader label={STR[lang].ui.model} />
                    <ColHeader label={STR[lang].ui.vendor} />
                    <ColHeader
                      label={
                        <span>
                          {view === "yearly" ? STR[lang].ui.baseYr : STR[lang].ui.baseMo}
                          <InfoDot title={T.help.base} />
                        </span>
                      }
                    />
                    <ColHeader
                      label={
                        <span>
                          {STR[lang].ui.included}
                          <InfoDot title={T.help.included} />
                        </span>
                      }
                    />
                    <ColHeader
                      label={
                        <span>
                          {STR[lang].ui.overIn}
                          <InfoDot title={T.help.overIn} />
                        </span>
                      }
                    />
                    <ColHeader
                      label={
                        <span>
                          {STR[lang].ui.overOut}
                          <InfoDot title={T.help.overOut} />
                        </span>
                      }
                    />
                    <ColHeader
                      label={
                        <span>
                          {view === "yearly" ? STR[lang].ui.estUsdYr : STR[lang].ui.estUsdMo}
                          <InfoDot title={T.help.est} />
                        </span>
                      }
                    />
                    <ColHeader
                      label={
                        <span>
                          {view === "yearly" ? STR[lang].ui.estKrwYr : STR[lang].ui.estKrwMo}
                          <InfoDot title={T.help.est} />
                        </span>
                      }
                    />
                    <ColHeader label={T.referral} />
                  </tr>
                </thead>
                <tbody>
                  {data.length === 0 && (
                    <tr>
                      <td className="py-6 text-center text-sm text-gray-500" colSpan={12}>
                        {T.empty}
                      </td>
                    </tr>
                  )}
                  {data.map((m) => {
                    const baseUSD = view === "yearly" ? (m.yearlyBaseUSD || 0) : (m.monthlyBaseUSD || 0);
                    const included = `${m.freeInKPerMonth || 0} / ${m.freeOutKPerMonth || 0}`;
                    const overIn = (m.overageInputUSDPerK ?? m.inputUSDPerK ?? 0).toFixed(3);
                    const overOut = (m.overageOutputUSDPerK ?? m.outputUSDPerK ?? 0).toFixed(3);
                    const estUSD = computePlanCostUSD(m, inK, outK, view);
                    const estKRW = Math.round(estUSD * fx);

                    return (
                      <tr key={m.name} className="border-t">
                        <Cell>{m.name}</Cell>
                        <Cell>{m.vendor}</Cell>
                        <Cell>
                          <div className="flex items-center gap-2">
                            <span>{baseUSD ? `$${baseUSD.toFixed(2)}` : "-"}</span>
                            <button
                              className="text-xs px-2 py-1 border rounded-md hover:bg-gray-50"
                              onClick={async () => {
                                const fresh = await fetchOfficialPrice(m);
                                m.monthlyBaseUSD = fresh.monthlyBaseUSD;
                                m.yearlyBaseUSD = fresh.yearlyBaseUSD;
                                m.freeInKPerMonth = fresh.freeInKPerMonth;
                                m.freeOutKPerMonth = fresh.freeOutKPerMonth;
                                m.overageInputUSDPerK = fresh.overageInputUSDPerK;
                                m.overageOutputUSDPerK = fresh.overageOutputUSDPerK;
                                alert("동기화 완료(데모): 크롤러를 연결하면 실가격으로 갱신됩니다.");
                              }}
                              title="공식 페이지에서 가격 동기화"
                            >
                              ↻
                            </button>
                          </div>
                        </Cell>
                        <Cell>{included}</Cell>
                        <Cell>{overIn}</Cell>
                        <Cell>{overOut}</Cell>
                        <Cell>{`$${estUSD.toFixed(2)}`}</Cell>
                        <Cell>{`₩${estKRW.toLocaleString()}`}</Cell>
                        <Cell>
                          <a
                            href={m.official}
                            target="_blank"
                            rel="noreferrer"
                            className="text-blue-600 underline"
                          >
                            {T.referral}
                          </a>
                        </Cell>
                      </tr>
                    );
                  })}
                </tbody>
              </table>
            )}
          </CardContent>
        </Card>

        {/* Calculator */}
        <Card className="mt-6">
          <CardHeader>
            <div className="flex items-center justify-between">
              <h3 className="text-base md:text-lg font-semibold">{T.calcTitle}</h3>
              <div className="flex items-center gap-2">
                <span className="text-xs text-gray-600">{T.ui.billing}</span>
                <Select value={view} onChange={(e) => setView(e.target.value as any)} className="w-[140px]">
                  <option value="token">{T.ui.tokenView}</option>
                  <option value="monthly">{T.ui.monthlyView}</option>
                  <option value="yearly">{T.ui.yearlyView}</option>
                </Select>
              </div>
            </div>
          </CardHeader>
          <CardContent>
            <div className="grid md:grid-cols-5 gap-3">
              <div>
                <label className="text-xs text-gray-600">{T.calcModel}</label>
                <Select value={calcModelName} onChange={(e) => setCalcModelName(e.target.value)}>
                  <option value="">—</option>
                  {MODEL_DATA.map((m) => (
                    <option key={m.name} value={m.name}>
                      {m.name}
                    </option>
                  ))}
                </Select>
              </div>
              <div>
                <label className="text-xs text-gray-600">
                  {view === "yearly" ? "연 Input 토큰(K)" : T.calcIn}
                </label>
                <Input
                  type="number"
                  value={view === "yearly" ? inK * 12 : inK}
                  onChange={(e) => {
                    const v = Number(e.target.value) || 0;
                    setInK(view === "yearly" ? v / 12 : v);
                  }}
                />
              </div>
              <div>
                <label className="text-xs text-gray-600">
                  {view === "yearly" ? "연 Output 토큰(K)" : T.calcOut}
                </label>
                <Input
                  type="number"
                  value={view === "yearly" ? outK * 12 : outK}
                  onChange={(e) => {
                    const v = Number(e.target.value) || 0;
                    setOutK(view === "yearly" ? v / 12 : v);
                  }}
                />
              </div>
              {calcModel && view !== "token" && (
                <div className="text-xs text-gray-600 flex flex-col justify-end">
                  <div className="px-3 py-2 rounded-xl bg-gray-50 border">
                    포함량(월): In {calcModel.freeInKPerMonth || 0}K / Out {calcModel.freeOutKPerMonth || 0}K
                  </div>
                </div>
              )}
              <div className="flex items-end">
                <Button className="w-full bg-blue-600 text-white" onClick={() => {}}>
                  {T.calcBtn}
                </Button>
              </div>
            </div>
            <div className="mt-4 text-sm">
              <div className="flex flex-wrap gap-4">
                <div className="px-4 py-2 rounded-xl bg-gray-50 border">
                  <span className="text-gray-600 mr-2">
                    {view === "yearly" ? "USD / yr" : view === "monthly" ? "USD / mo" : `${T.usd} ${T.perMonth}`}
                  </span>
                  <span className="font-semibold">${costUSD.toFixed(2)}</span>
                </div>
                <div className="px-4 py-2 rounded-xl bg-gray-50 border">
                  <span className="text-gray-600 mr-2">
                    {view === "yearly" ? "KRW / yr" : view === "monthly" ? "KRW / mo" : `${T.krw} ${T.perMonth}`}
                  </span>
                  <span className="font-semibold">₩{costKRW.toLocaleString()}</span>
                </div>
              </div>
            </div>
          </CardContent>
        </Card>

        {/* Footer note */}
        <div className="text-xs text-gray-500 mt-6">
          <p>※ 모든 가격은 예시이며, 실제 가격은 각 공식 페이지를 확인해 주세요. 환율은 임의 입력값을 사용합니다.</p>
        </div>
      </div>
    </div>
  );
}
