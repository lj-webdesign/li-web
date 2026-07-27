// /api/contact.js
// Vercel Serverless Function — お問い合わせフォームの送信処理
//
// 【必要な環境変数】Vercelのプロジェクト設定 > Environment Variables に設定してください。
//   RESEND_API_KEY   … Resend (https://resend.com) で発行したAPIキー
//   CONTACT_TO_EMAIL … 通知を受け取りたい宛先メールアドレス（例: ljweb@liquid-japan.com）
//   CONTACT_FROM_EMAIL … 送信元アドレス（Resendで送信ドメイン認証が必要。例: noreply@lj-webdesign.com）
//
// 【Resend側で必要な設定】
//   1. https://resend.com でアカウント作成
//   2. 送信元に使うドメイン（lj-webdesign.com）をResendに追加し、DNSレコード(SPF/DKIM)を設定
//   3. API Keyを発行し、上記の環境変数に設定
//
// ※ドメイン認証が完了するまでは、Resendのテスト用アドレス(onboarding@resend.dev)からのみ送信可能です。

const RATE_LIMIT_MIN_SECONDS = 3; // フォーム表示から3秒未満での送信はボット疑いとして拒否

function isValidEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(String(email || ""));
}

async function sendViaResend(payload) {
  const res = await fetch("https://api.resend.com/emails", {
    method: "POST",
    headers: {
      Authorization: `Bearer ${process.env.RESEND_API_KEY}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify(payload),
  });
  if (!res.ok) {
    const detail = await res.text().catch(() => "");
    throw new Error(`Resend API error: ${res.status} ${detail}`);
  }
  return res.json();
}

export default async function handler(req, res) {
  res.setHeader("Access-Control-Allow-Origin", "https://www.lj-webdesign.com");
  res.setHeader("Access-Control-Allow-Methods", "POST, OPTIONS");
  res.setHeader("Access-Control-Allow-Headers", "Content-Type");

  if (req.method === "OPTIONS") return res.status(204).end();
  if (req.method !== "POST") {
    return res.status(405).json({ ok: false, error: "method_not_allowed" });
  }

  if (!process.env.RESEND_API_KEY || !process.env.CONTACT_TO_EMAIL || !process.env.CONTACT_FROM_EMAIL) {
    console.error("Missing required environment variables for /api/contact");
    return res.status(500).json({ ok: false, error: "server_not_configured" });
  }

  let body = req.body;
  if (typeof body === "string") {
    try { body = JSON.parse(body); } catch { body = {}; }
  }
  body = body || {};

  const name = String(body.name || "").trim();
  const email = String(body.email || "").trim();
  const business = String(body.business || "").trim();
  const message = String(body.message || "").trim();
  const consent = !!body.consent;
  const honeypot = String(body.website || "").trim(); // 人間には見えない隠しフィールド
  const startedAt = Number(body.startedAt || 0);

  // --- スパム対策 ---
  if (honeypot) {
    // ボットがhoneypotに入力した場合は、成功したフリをして静かに破棄する
    return res.status(200).json({ ok: true });
  }
  if (startedAt && (Date.now() - startedAt) / 1000 < RATE_LIMIT_MIN_SECONDS) {
    return res.status(400).json({ ok: false, error: "too_fast" });
  }

  // --- 必須項目チェック ---
  if (!name || !email || !message) {
    return res.status(400).json({ ok: false, error: "missing_fields" });
  }
  if (!isValidEmail(email)) {
    return res.status(400).json({ ok: false, error: "invalid_email" });
  }
  if (!consent) {
    return res.status(400).json({ ok: false, error: "consent_required" });
  }

  const safe = (s) => String(s).replace(/[<>]/g, "");

  try {
    // 1) 管理者への通知メール
    await sendViaResend({
      from: `LJ WEB DESIGN お問い合わせ <${process.env.CONTACT_FROM_EMAIL}>`,
      to: process.env.CONTACT_TO_EMAIL,
      reply_to: email,
      subject: `【無料相談】${safe(name)} 様よりお問い合わせ`,
      text:
        `お名前: ${safe(name)}\n` +
        `業種: ${safe(business) || "未選択"}\n` +
        `メール: ${safe(email)}\n\n` +
        `ご相談内容:\n${safe(message)}`,
    });

    // 2) 送信者への自動返信
    await sendViaResend({
      from: `LJ WEB DESIGN <${process.env.CONTACT_FROM_EMAIL}>`,
      to: email,
      subject: "お問い合わせありがとうございます｜LJ WEB DESIGN",
      text:
        `${safe(name)} 様\n\n` +
        `この度はLJ WEB DESIGNへお問い合わせいただき、誠にありがとうございます。\n` +
        `内容を確認の上、原則24時間以内に担当よりご連絡いたします。\n\n` +
        `▼お送りいただいた内容\n${safe(message)}\n\n` +
        `----------------------------\n` +
        `LJ WEB DESIGN by LIQUID JAPAN\n` +
        `https://www.lj-webdesign.com/\n` +
        `----------------------------`,
    });

    return res.status(200).json({ ok: true });
  } catch (err) {
    console.error("Contact form send failed:", err);
    return res.status(500).json({ ok: false, error: "send_failed" });
  }
}
