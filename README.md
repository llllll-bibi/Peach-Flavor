<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>字卡传讯 | 完整功能版</title>
    <style>
        :root {
            --theme-bg: #fef6f9; --theme-header: #fff0f3; --theme-bubble-user: #ffb7c5;
            --theme-bubble-ai: #ffffff; --theme-text-user: #ffffff; --theme-text-ai: #ad5a5a;
            --theme-accent: #ff8a9f; --theme-border: #ffe0e4; --theme-input-bg: #fff5f7;
            --theme-status: #ad5a5a; --theme-card-bg: #ffffff;
            --theme-font: -apple-system, 'SF Pro Text', 'Helvetica Neue', Roboto, sans-serif;
        }
        * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: var(--theme-font); }
        body { background: #fce4ec; display: flex; justify-content: center; align-items: center; min-height: 100vh; }
        .wechat { width: 100%; max-width: 430px; height: 780px; background: var(--theme-card-bg); display: flex; flex-direction: column; border-radius: 32px; overflow: hidden; box-shadow: 0 8px 28px rgba(0,0,0,0.08); position: relative; }
        .status-bar { background: var(--theme-header); padding: 8px 20px 4px; display: flex; justify-content: space-between; font-size: 12px; color: var(--theme-status); border-bottom: 0.5px solid var(--theme-border); font-weight: 500; }
        .chat-header { background: var(--theme-header); padding: 12px 16px; display: flex; align-items: center; gap: 12px; border-bottom: 0.5px solid var(--theme-border); }
        .back-icon { font-size: 24px; color: var(--theme-accent); cursor: pointer; }
        .avatar-header { width: 36px; height: 36px; border-radius: 50%; background: var(--theme-border); display: flex; align-items: center; justify-content: center; font-size: 22px; cursor: pointer; box-shadow: 0 2px 6px rgba(0,0,0,0.05); overflow: hidden; }
        .header-info { flex: 1; cursor: pointer; }
        .header-name { font-weight: 650; font-size: 18px; color: var(--theme-status); }
        .header-status { font-size: 11px; color: var(--theme-accent); display: flex; align-items: center; gap: 4px; }
        .menu-icon { font-size: 22px; color: var(--theme-accent); cursor: pointer; }
        .chat-main { flex: 1; overflow-y: auto; padding: 16px 12px; background: var(--theme-bg); display: flex; flex-direction: column; gap: 12px; background-size: cover; background-position: center; position: relative; }
        .date-divider { text-align: center; font-size: 10px; color: var(--theme-accent); margin: 8px 0; opacity: 0.7; }
        .msg-row { display: flex; align-items: flex-start; gap: 8px; max-width: 85%; }
        .msg-row.user { align-self: flex-end; flex-direction: row-reverse; }
        .msg-row.ai { align-self: flex-start; }
        .avatar { width: 38px; height: 38px; border-radius: 50%; background: var(--theme-border); display: flex; align-items: center; justify-content: center; font-size: 20px; flex-shrink: 0; box-shadow: 0 2px 4px rgba(0,0,0,0.02); cursor: pointer; overflow: hidden; }
        .bubble { padding: 8px 14px; border-radius: 20px; font-size: 15px; line-height: 1.45; word-break: break-word; max-width: 260px; position: relative; }
        .user .bubble { background: var(--theme-bubble-user); color: var(--theme-text-user); border-top-right-radius: 4px; }
        .ai .bubble { background: var(--theme-bubble-ai); color: var(--theme-text-ai); border-top-left-radius: 4px; box-shadow: 0 2px 6px rgba(0,0,0,0.04); }
        .quote-block { font-size: 12px; color: #8e8e93; border-left: 3px solid #c29ebf; padding-left: 8px; margin-bottom: 6px; word-break: break-all; }
        .quote-block .quote-sender { font-size: 11px; color: #999; margin-bottom: 2px; }
        .image-msg { max-width: 200px; border-radius: 16px; overflow: hidden; }
        .image-msg img { width: 100%; display: block; }
        .msg-time { font-size: 10px; color: var(--theme-accent); margin-top: 4px; margin-left: 44px; opacity: 0.7; }
        .user .msg-time { text-align: right; margin-right: 8px; margin-left: 0; }
        .system-message { text-align: center; font-size: 12px; color: #b0b0b5; margin: 8px 0; font-style: italic; background: transparent; }
        .typing-indicator { display: flex; align-items: center; gap: 8px; margin: 8px 0; background: rgba(255,255,255,0.9); padding: 8px 12px; border-radius: 20px; width: fit-content; max-width: 80%; box-shadow: 0 1px 2px rgba(0,0,0,0.05); }
        .typing-avatar { width: 28px; height: 28px; border-radius: 50%; background: var(--theme-border); display: flex; align-items: center; justify-content: center; font-size: 16px; overflow: hidden; }
        .typing-dots { display: flex; gap: 3px; align-items: center; }
        .typing-dot { width: 6px; height: 6px; background: #aaa; border-radius: 50%; animation: typing-wave 1.4s infinite; }
        .typing-dot:nth-child(2) { animation-delay: 0.2s; }
        .typing-dot:nth-child(3) { animation-delay: 0.4s; }
        @keyframes typing-wave { 0%, 60%, 100% { transform: translateY(0); opacity: 0.4; } 30% { transform: translateY(-4px); opacity: 1; } }
        .input-bar { background: var(--theme-header); padding: 8px 12px 12px; border-top: 0.5px solid var(--theme-border); display: flex; align-items: center; gap: 10px; position: relative; }
        .input-field { flex: 1; background: var(--theme-input-bg); border: none; border-radius: 28px; padding: 10px 16px; font-size: 15px; outline: none; color: var(--theme-status); }
        .send-btn { background: var(--theme-accent); border: none; border-radius: 28px; padding: 8px 20px; color: white; font-weight: 600; cursor: pointer; font-size: 14px; }
        .icon-btn { background: none; border: none; font-size: 24px; cursor: pointer; color: var(--theme-accent); }
        .quote-indicator { background: var(--theme-input-bg); border-radius: 20px; padding: 8px 12px; font-size: 12px; color: var(--theme-status); margin: 0 12px 6px 12px; display: flex; justify-content: space-between; align-items: center; border: 0.5px solid var(--theme-border); }
        .cancel-quote { background: none; border: none; font-size: 16px; cursor: pointer; color: var(--theme-accent); padding: 0 4px; }

        .more-menu { position: absolute; bottom: 56px; left: 8px; background: white; border-radius: 20px; padding: 12px; z-index: 160; box-shadow: 0 4px 20px rgba(0,0,0,0.15); display: none; flex-direction: column; gap: 8px; min-width: 160px; }
        .more-menu.show { display: flex; }
        .more-menu-item { display: flex; align-items: center; gap: 10px; padding: 10px 14px; border-radius: 14px; cursor: pointer; font-size: 14px; color: var(--theme-status); font-weight: 500; }
        .more-menu-item:active { background: var(--theme-input-bg); }

        .tab-bar { background: var(--theme-header); display: flex; border-top: 0.5px solid var(--theme-border); padding: 8px 0 10px; }
        .tab-item { flex: 1; text-align: center; font-size: 11px; color: var(--theme-accent); cursor: pointer; opacity: 0.7; }
        .tab-item.active { color: var(--theme-accent); opacity: 1; font-weight: 600; }
        .tab-icon { font-size: 22px; display: block; margin-bottom: 2px; }

        .card-pane, .moment-pane, .couple-pane, .letter-pane, .settings-tab-pane { flex: 1; overflow-y: auto; display: none; background: var(--theme-bg); padding: 16px; }
        .moment-pane { background-size: cover; background-position: center; transition: background-image 0.2s; }

        .card-group { background: var(--theme-card-bg); border-radius: 24px; padding: 16px; margin-bottom: 16px; box-shadow: 0 4px 12px rgba(0,0,0,0.02); }
        .card-group-title { font-weight: 600; display: flex; justify-content: space-between; margin-bottom: 12px; color: var(--theme-status); }
        .card-items { display: flex; flex-direction: column; gap: 8px; }
        .card-chip { background: var(--theme-input-bg); padding: 10px 14px; border-radius: 28px; display: flex; justify-content: space-between; align-items: center; border: 0.5px solid var(--theme-border); cursor: pointer; transition: 0.1s; color: var(--theme-status); }
        .batch-import-area { margin-top: 12px; border-top: 1px solid var(--theme-border); padding-top: 12px; }
        .batch-textarea { width: 100%; border: 0.5px solid var(--theme-border); border-radius: 20px; padding: 10px; margin-bottom: 8px; background: var(--theme-input-bg); color: var(--theme-status); }
        .batch-import-btn { background: #c29ebf; border: none; border-radius: 30px; padding: 6px 14px; font-size: 12px; color: white; cursor: pointer; }
        .delete-group-btn { background: #ff7b8b; border: none; border-radius: 30px; padding: 4px 12px; font-size: 12px; color: white; cursor: pointer; }
        .delete-card { color: #ff7b8b; margin-left: 8px; cursor: pointer; font-size: 14px; }

        .moment-item { background: var(--theme-card-bg); border-radius: 32px; padding: 18px; margin-bottom: 16px; box-shadow: 0 6px 16px rgba(255, 120, 140, 0.08); position: relative; border: 1px solid var(--theme-border); }
        .moment-header { display: flex; gap: 12px; align-items: center; }
        .moment-avatar { width: 52px; height: 52px; border-radius: 50%; background: var(--theme-border); display: flex; align-items: center; justify-content: center; font-size: 32px; overflow: hidden; }
        .moment-name { font-weight: 650; font-size: 16px; color: var(--theme-status); }
        .moment-time { font-size: 10px; color: var(--theme-accent); margin-top: 2px; opacity: 0.7; }
        .moment-content { margin: 14px 0 14px 64px; font-size: 15px; color: var(--theme-status); line-height: 1.45; background: var(--theme-input-bg); padding: 8px 14px; border-radius: 24px; position: relative; }
        .moment-sticker { position: absolute; right: -8px; top: -12px; font-size: 28px; transform: rotate(12deg); filter: drop-shadow(0 2px 4px rgba(0,0,0,0.1)); pointer-events: none; }
        .moment-actions { margin-left: 64px; display: flex; gap: 20px; font-size: 13px; margin-top: 8px; }
        .like-btn, .comment-toggle { cursor: pointer; color: var(--theme-accent); display: inline-flex; align-items: center; gap: 4px; }
        .delete-moment { position: absolute; top: 12px; right: 12px; background: none; border: none; font-size: 18px; color: var(--theme-accent); cursor: pointer; opacity: 0.7; }
        .delete-moment:hover { opacity: 1; color: #ff4d6d; }
        .comment-list { margin: 12px 0 0 64px; background: var(--theme-input-bg); border-radius: 28px; padding: 12px 16px; }
        .comment-item { font-size: 13px; margin-bottom: 8px; display: flex; justify-content: space-between; align-items: center; color: var(--theme-status); }
        .comment-reply { color: #c29ebf; cursor: pointer; font-size: 11px; }
        .comment-input-area { margin-left: 64px; margin-top: 12px; display: flex; gap: 10px; }
        .comment-input { flex:1; border: 0.5px solid var(--theme-border); border-radius: 30px; padding: 8px 14px; font-size: 12px; background: white; color: var(--theme-status); }
        .publish-area { background: var(--theme-header); border-radius: 32px; padding: 16px; margin-bottom: 16px; border: 1px dashed var(--theme-accent); }
        .publish-textarea { width: 100%; border: 0.5px solid var(--theme-border); border-radius: 28px; padding: 12px; background: white; color: var(--theme-status); resize: none; }
        .post-btn { background: var(--theme-accent); border: none; border-radius: 30px; padding: 8px 20px; color: white; font-weight: 600; margin-top: 10px; cursor: pointer; }

        .couple-header, .letter-item, .write-letter-area { background: var(--theme-card-bg); border-radius: 28px; padding: 16px; margin-bottom: 16px; border: 0.5px solid var(--theme-border); }
        .letter-item { position: relative; }
        .delete-letter { position: absolute; top: 12px; right: 12px; background: none; border: none; font-size: 16px; color: var(--theme-accent); cursor: pointer; opacity: 0.6; z-index: 2; }
        .delete-letter:hover { opacity: 1; color: #ff4d6d; }
        .delay-settings { background: var(--theme-input-bg); border-radius: 24px; padding: 12px; margin-bottom: 16px; }
        .delay-slider { display: flex; gap: 12px; align-items: center; flex-wrap: wrap; margin-top: 8px; }
        .letter-header { background: var(--theme-header); display: flex; align-items: center; gap: 12px; }
        .letter-avatar { width: 52px; height: 52px; border-radius: 50%; background: var(--theme-border); display: flex; align-items: center; justify-content: center; font-size: 28px; overflow: hidden; }
        .write-letter-input, .write-letter-textarea { width: 100%; border: 0.5px solid var(--theme-border); border-radius: 28px; padding: 10px 14px; margin-bottom: 10px; background: var(--theme-input-bg); color: var(--theme-status); }

        .call-float { position: fixed; background: rgba(0,0,0,0.85); backdrop-filter: blur(24px); border-radius: 48px; z-index: 200; display: flex; align-items: center; gap: 10px; padding: 6px 16px; cursor: move; box-shadow: 0 4px 20px rgba(0,0,0,0.3); }
        .call-float.large { width: 200px; height: 70px; border-radius: 35px; }
        .call-float.medium { width: 160px; height: 56px; border-radius: 28px; }
        .call-avatar-mini { width: 40px; height: 40px; border-radius: 50%; background: #3a3a3c; display: flex; align-items: center; justify-content: center; font-size: 20px; overflow: hidden; }
        .call-info { color: white; flex: 1; font-size: 14px; font-weight: 500; text-align: center; }
        .call-hangup-float { background: #ff5e5e; border: none; border-radius: 30px; padding: 4px 12px; color: white; cursor: pointer; font-size: 12px; font-weight: 600; }

        .emoji-picker { position: fixed; bottom: 80px; left: 10px; right: 10px; background: white; border-radius: 28px; padding: 12px; z-index: 150; box-shadow: 0 4px 20px rgba(0,0,0,0.2); max-height: 300px; overflow-y: auto; }
        .emoji-picker .emoji-grid { display: flex; flex-wrap: wrap; gap: 12px; justify-content: center; }
        .emoji-item { width: 70px; height: 70px; object-fit: contain; cursor: pointer; border-radius: 16px; background: #f8f8f8; padding: 4px; }

        .settings-panel, .overlay { position: fixed; bottom: 0; left: 0; right: 0; background: var(--theme-card-bg); border-radius: 32px 32px 0 0; padding: 24px 20px; z-index: 20; transform: translateY(100%); transition: 0.3s; max-width: 430px; margin: 0 auto; box-shadow: 0 -4px 20px rgba(0,0,0,0.08); max-height: 85vh; overflow-y: auto; }
        .settings-panel.open { transform: translateY(0); }
        .overlay { background: rgba(0,0,0,0.4); z-index: 19; display: none; }
        .overlay.show { display: block; }
        .setting-item { display: flex; justify-content: space-between; align-items: center; padding: 12px 0; border-bottom: 0.5px solid #f0f0f0; flex-wrap: wrap; gap: 8px; }
        .setting-item label { font-size: 14px; color: var(--theme-status); }
        .setting-item input { width: 80px; border-radius: 20px; border: 0.5px solid var(--theme-border); padding: 4px 8px; background: var(--theme-input-bg); color: var(--theme-status); }
        .close-settings { text-align: center; margin-top: 20px; color: var(--theme-accent); font-weight: 600; cursor: pointer; }
        .emoji-management { margin-top: 12px; display: flex; flex-wrap: wrap; gap: 8px; }
        .emoji-thumb { width: 60px; height: 60px; object-fit: contain; margin: 4px; border-radius: 12px; cursor: pointer; }
        .context-menu { position: fixed; background: #2c2c2e; border-radius: 14px; padding: 8px 0; min-width: 140px; z-index: 300; box-shadow: 0 4px 12px rgba(0,0,0,0.3); backdrop-filter: blur(20px); }
        .context-menu div { padding: 10px 16px; font-size: 14px; color: white; cursor: pointer; display: flex; align-items: center; gap: 6px; }
        .context-menu div:active { background: #3a3a3c; }

        .bubble-style-square .user .bubble, .bubble-style-square .ai .bubble { border-radius: 4px !important; }
        .bubble-style-gradient .user .bubble { background: linear-gradient(135deg, var(--theme-bubble-user), var(--theme-accent)) !important; }
        .bubble-style-outline .user .bubble { background: transparent !important; border: 2px solid var(--theme-bubble-user); color: var(--theme-bubble-user) !important; }
        .bubble-style-outline .ai .bubble { background: transparent !important; border: 2px solid var(--theme-border); color: var(--theme-text-ai) !important; box-shadow: none; }
        .bubble-style-shadow .user .bubble { box-shadow: 0 4px 16px rgba(255,100,130,0.35) !important; }
        .bubble-style-shadow .ai .bubble { box-shadow: 0 4px 16px rgba(0,0,0,0.1) !important; }
        .bubble-style-heart .user .bubble { border-radius: 20px 20px 4px 20px !important; }
        .bubble-style-heart .ai .bubble { border-radius: 20px 20px 20px 4px !important; }
        .font-handwriting { --theme-font: 'Segoe Script', 'Bradley Hand', 'Snell Roundhand', cursive; }
        .font-cute { --theme-font: 'Comic Sans MS', '华文彩云', '圆体', 'PingFang SC', fantasy; }
        .font-kaiti { --theme-font: 'KaiTi', 'STKaiti', 'BiauKai', '楷体', serif; }

        .feature-panel { position: fixed; bottom: 0; left: 0; right: 0; background: var(--theme-card-bg); border-radius: 32px 32px 0 0; padding: 24px 20px; z-index: 25; transform: translateY(100%); transition: 0.3s; max-width: 430px; margin: 0 auto; box-shadow: 0 -4px 20px rgba(0,0,0,0.08); height: 85vh; overflow-y: auto; }
        .feature-panel.open { transform: translateY(0); }
        .feature-title { font-size: 18px; font-weight: 700; color: var(--theme-status); text-align: center; margin-bottom: 16px; }
        .close-feature { text-align: center; margin-top: 20px; color: var(--theme-accent); font-weight: 600; cursor: pointer; font-size: 15px; }

        .theme-grid { display: grid; grid-template-columns: repeat(5, 1fr); gap: 10px; margin: 12px 0; }
        .theme-block { width: 100%; aspect-ratio: 1; border-radius: 16px; cursor: pointer; border: 3px solid transparent; display: flex; align-items: center; justify-content: center; font-size: 12px; color: white; text-shadow: 0 1px 2px rgba(0,0,0,0.3); font-weight: 600; }
        .theme-block.active { border-color: var(--theme-accent); transform: scale(1.1); }
        .style-grid { display: flex; gap: 10px; overflow-x: auto; padding: 8px 0; }
        .style-block { min-width: 80px; height: 50px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 12px; cursor: pointer; border: 2px solid var(--theme-border); background: var(--theme-input-bg); color: var(--theme-status); font-weight: 600; }
        .style-block.active { border-color: var(--theme-accent); background: var(--theme-header); }
        .font-grid { display: flex; gap: 10px; flex-wrap: wrap; }
        .font-block { padding: 8px 16px; border-radius: 20px; border: 2px solid var(--theme-border); cursor: pointer; font-size: 14px; color: var(--theme-status); background: var(--theme-input-bg); }
        .font-block.active { border-color: var(--theme-accent); background: var(--theme-header); }

        .fortune-card { background: var(--theme-header); border-radius: 24px; padding: 24px; text-align: center; margin: 16px 0; border: 1px solid var(--theme-border); }
        .fortune-title { font-size: 28px; font-weight: 700; color: var(--theme-accent); margin-bottom: 12px; }
        .fortune-content { font-size: 16px; color: var(--theme-status); line-height: 1.6; margin-bottom: 12px; }
        .fortune-advice { font-size: 14px; color: var(--theme-accent); font-style: italic; }
        .fortune-btn { background: var(--theme-accent); color: white; border: none; border-radius: 30px; padding: 12px 36px; font-size: 16px; cursor: pointer; margin-top: 16px; font-weight: 600; width: 100%; }

        .focus-timer { 
            font-size: 38px; font-weight: 700; color: var(--theme-accent); text-align: center; margin: 16px 0; 
            font-variant-numeric: tabular-nums; letter-spacing: 4px;
            text-shadow: 0 2px 10px rgba(255,138,159,0.35);
            font-family: 'Comic Sans MS', '华文彩云', '圆体', 'PingFang SC', fantasy;
        }
        .focus-input { width: 100%; border: 0.5px solid var(--theme-border); border-radius: 28px; padding: 12px 16px; margin-bottom: 12px; background: var(--theme-input-bg); color: var(--theme-status); font-size: 15px; }
        .focus-btn-group { display: flex; gap: 12px; justify-content: center; margin-top: 16px; }
        .focus-btn { border: none; border-radius: 30px; padding: 12px 28px; color: white; font-weight: 600; cursor: pointer; font-size: 15px; }
        .focus-start { background: #22c55e; }
        .focus-pause { background: #f59e0b; }
        .focus-stop { background: #ef4444; }
        .focus-status { text-align: center; color: var(--theme-accent); margin: 12px 0; font-size: 14px; font-weight: 500; }

        .focus-couple-bar { display: flex; justify-content: center; align-items: center; gap: 20px; margin-bottom: 16px; padding: 12px; background: var(--theme-header); border-radius: 24px; border: 1px solid var(--theme-border); }
        .focus-couple-avatar { width: 48px; height: 48px; border-radius: 50%; overflow: hidden; border: 2px solid var(--theme-accent); background: var(--theme-border); display: flex; align-items: center; justify-content: center; font-size: 24px; }
        .focus-couple-avatar img { width: 100%; height: 100%; object-fit: cover; }
        .focus-couple-info { text-align: center; }
        .focus-couple-name { font-size: 13px; font-weight: 600; color: var(--theme-status); }
        .focus-couple-heart { font-size: 20px; color: var(--theme-accent); }

        .period-input { width: 100%; border: 0.5px solid var(--theme-border); border-radius: 20px; padding: 12px; margin: 8px 0; background: var(--theme-input-bg); color: var(--theme-status); font-size: 15px; }
        .period-info { background: var(--theme-header); border-radius: 24px; padding: 20px; margin: 16px 0; text-align: center; border: 1px solid var(--theme-border); }
        .period-phase { font-size: 22px; font-weight: 700; color: var(--theme-accent); margin-bottom: 8px; }
        .period-countdown { font-size: 14px; color: var(--theme-status); line-height: 1.6; }

        .game-option { background: var(--theme-header); border-radius: 20px; padding: 16px; margin: 10px 0; cursor: pointer; border: 1px solid var(--theme-border); text-align: center; color: var(--theme-status); transition: 0.1s; font-weight: 600; font-size: 15px; }
        .game-option:active { transform: scale(0.98); background: var(--theme-border); }
        .game-question { font-size: 16px; font-weight: 600; color: var(--theme-status); margin: 16px 0; text-align: center; line-height: 1.5; }
        .game-result { background: var(--theme-header); border-radius: 20px; padding: 16px; margin: 12px 0; text-align: center; color: var(--theme-accent); font-weight: 600; font-size: 15px; border: 1px solid var(--theme-border); }
        .game-btn { background: var(--theme-accent); color: white; border: none; border-radius: 24px; padding: 10px 20px; cursor: pointer; font-weight: 600; margin: 6px; font-size: 14px; }

        .water-cups { display: flex; justify-content: center; gap: 10px; margin: 16px 0; flex-wrap: wrap; }
        .water-cup { width: 44px; height: 44px; border-radius: 50%; border: 2px solid var(--theme-border); display: flex; align-items: center; justify-content: center; font-size: 20px; cursor: pointer; background: var(--theme-input-bg); transition: 0.2s; }
        .water-cup.filled { background: var(--theme-accent); border-color: var(--theme-accent); color: white; }
        .water-settings { display: flex; gap: 16px; align-items: center; margin: 12px 0; flex-wrap: wrap; justify-content: center; }
        .water-settings label { font-size: 13px; color: var(--theme-status); font-weight: 500; }
        .water-settings input { width: 60px; border: 0.5px solid var(--theme-border); border-radius: 16px; padding: 6px 10px; background: var(--theme-input-bg); color: var(--theme-status); text-align: center; }
        .water-remind-btn { background: var(--theme-accent); color: white; border: none; border-radius: 30px; padding: 10px 24px; cursor: pointer; font-weight: 600; font-size: 14px; }
        .water-status { text-align: center; color: var(--theme-status); margin: 8px 0; font-size: 15px; font-weight: 600; }

        .call-screen { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: #1a1a1a; z-index: 250; display: none; flex-direction: column; align-items: center; justify-content: center; background-size: cover; background-position: center; }
        .call-screen.show { display: flex; }
        .call-screen-bg { position: absolute; top: 0; left: 0; right: 0; bottom: 0; background-size: cover; background-position: center; filter: blur(20px) brightness(0.4); z-index: 0; }
        .call-screen-content { position: relative; z-index: 1; text-align: center; color: white; }
        .call-screen-avatar { width: 120px; height: 120px; border-radius: 50%; background: rgba(255,255,255,0.2); display: flex; align-items: center; justify-content: center; font-size: 60px; margin: 0 auto 20px; overflow: hidden; border: 3px solid rgba(255,255,255,0.3); }
        .call-screen-name { font-size: 24px; font-weight: 600; margin-bottom: 8px; }
        .call-screen-status { font-size: 14px; opacity: 0.8; margin-bottom: 60px; }
        .call-screen-btns { display: flex; gap: 40px; justify-content: center; margin-top: 40px; }
        .call-screen-btn { width: 70px; height: 70px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 28px; cursor: pointer; border: none; color: white; }
        .call-accept { background: #22c55e; }
        .call-reject { background: #ef4444; }
        .call-mute { background: rgba(255,255,255,0.2); }
        .call-speaker { background: rgba(255,255,255,0.2); }

        .red-bubble { background: #fa5151 !important; color: white !important; padding: 10px 14px !important; display: flex; align-items: center; gap: 8px; cursor: pointer; }
        .red-bubble .red-icon { font-size: 28px; }
        .red-bubble .red-text { font-size: 13px; }
        .red-bubble .red-sub { font-size: 10px; opacity: 0.8; }
        .transfer-bubble { background: #fa9d3b !important; color: white !important; padding: 10px 14px !important; display: flex; align-items: center; gap: 8px; cursor: pointer; }
        
        .redpacket-overlay { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.6); z-index: 300; display: none; align-items: center; justify-content: center; }
        .redpacket-overlay.show { display: flex; }
        .redpacket-card { width: 300px; background: linear-gradient(180deg, #fa5151 0%, #d9363e 100%); border-radius: 16px; padding: 0; overflow: hidden; position: relative; }
        .redpacket-top { background: #fa5151; padding: 30px 20px 20px; text-align: center; color: white; }
        .redpacket-from { font-size: 16px; margin-bottom: 8px; opacity: 0.9; }
        .redpacket-bless { font-size: 20px; font-weight: 600; margin-bottom: 20px; }
        .redpacket-open-btn { width: 60px; height: 60px; border-radius: 50%; background: #ffd700; border: none; color: #d9363e; font-size: 14px; font-weight: 700; cursor: pointer; margin: 0 auto; display: block; transition: transform 0.3s; }
        .redpacket-open-btn.spinning { animation: spinOpen 0.6s linear; }
        @keyframes spinOpen { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .redpacket-amount { text-align: center; padding: 24px; background: white; display: none; }
        .redpacket-amount.show { display: block; }
        .redpacket-num { font-size: 36px; color: #d9363e; font-weight: 700; }
        .redpacket-unit { font-size: 14px; color: #666; }
        .redpacket-close { position: absolute; top: 10px; right: 10px; background: none; border: none; color: white; font-size: 20px; cursor: pointer; }

        .transfer-overlay { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.6); z-index: 300; display: none; align-items: center; justify-content: center; }
        .transfer-overlay.show { display: flex; }
        .transfer-card { width: 300px; background: white; border-radius: 16px; overflow: hidden; }
        .transfer-header { background: #fa9d3b; padding: 20px; color: white; text-align: center; }
        .transfer-amount-input { width: 100%; border: none; border-bottom: 2px solid #fa9d3b; padding: 16px; font-size: 32px; text-align: center; outline: none; color: #333; }
        .transfer-btn { width: 100%; background: #fa9d3b; color: white; border: none; padding: 14px; font-size: 16px; font-weight: 600; cursor: pointer; }

        .music-float { position: fixed; bottom: 100px; right: 10px; width: 56px; height: 56px; border-radius: 50%; background: linear-gradient(135deg, #ff6b6b, #feca57); z-index: 180; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 24px; box-shadow: 0 4px 12px rgba(0,0,0,0.2); animation: pulse 2s infinite; }
        @keyframes pulse { 0%, 100% { transform: scale(1); } 50% { transform: scale(1.05); } }
        .music-panel { position: fixed; bottom: 0; left: 0; right: 0; background: var(--theme-card-bg); border-radius: 32px 32px 0 0; padding: 24px 20px; z-index: 26; transform: translateY(100%); transition: 0.3s; max-width: 430px; margin: 0 auto; box-shadow: 0 -4px 20px rgba(0,0,0,0.08); height: 85vh; overflow-y: auto; }
        .music-panel.open { transform: translateY(0); }
        .vinyl-record { width: 200px; height: 200px; border-radius: 50%; margin: 20px auto; background: repeating-radial-gradient(#222, #222 10px, #333 10px, #333 20px); position: relative; box-shadow: 0 8px 24px rgba(0,0,0,0.3); display: flex; align-items: center; justify-content: center; }
        .vinyl-record.spinning { animation: spin 3s linear infinite; }
        @keyframes spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
        .vinyl-center { width: 70px; height: 70px; border-radius: 50%; background: var(--theme-accent); overflow: hidden; border: 4px solid #1a1a1a; cursor: pointer; }
        .vinyl-center img { width: 100%; height: 100%; object-fit: cover; }
        .music-together { display: flex; justify-content: center; align-items: center; gap: 20px; margin: 20px 0; }
        .music-together-avatar { width: 50px; height: 50px; border-radius: 50%; overflow: hidden; border: 2px solid var(--theme-accent); }
        .music-together-avatar img { width: 100%; height: 100%; object-fit: cover; }
        .music-song-item { background: var(--theme-input-bg); border-radius: 16px; padding: 12px; margin: 8px 0; display: flex; justify-content: space-between; align-items: center; border: 0.5px solid var(--theme-border); }
        .music-song-info { flex: 1; }
        .music-song-name { font-weight: 600; color: var(--theme-status); font-size: 14px; }
        .music-song-source { font-size: 11px; color: var(--theme-accent); opacity: 0.7; }
        .music-play-btn { background: var(--theme-accent); color: white; border: none; border-radius: 50%; width: 36px; height: 36px; cursor: pointer; font-size: 14px; margin-left: 8px; }
        .music-delete-btn { color: #ff7b8b; background: none; border: none; font-size: 16px; cursor: pointer; margin-left: 8px; }
        .music-add-area { background: var(--theme-header); border-radius: 20px; padding: 16px; margin: 12px 0; border: 1px dashed var(--theme-accent); }
        .music-input { width: 100%; border: 0.5px solid var(--theme-border); border-radius: 16px; padding: 10px; margin: 6px 0; background: var(--theme-input-bg); color: var(--theme-status); font-size: 14px; }
        .music-mini-vinyl { position: fixed; bottom: 160px; right: 10px; width: 44px; height: 44px; border-radius: 50%; background: repeating-radial-gradient(#222, #222 5px, #333 5px, #333 10px); z-index: 179; display: none; align-items: center; justify-content: center; box-shadow: 0 2px 8px rgba(0,0,0,0.2); }
        .music-mini-vinyl.show { display: flex; }
        .music-mini-vinyl.spinning { animation: spin 3s linear infinite; }
        .music-mini-center { width: 18px; height: 18px; border-radius: 50%; overflow: hidden; border: 2px solid #1a1a1a; }
        .music-mini-center img { width: 100%; height: 100%; object-fit: cover; }

        .music-list-scroll { max-height: 200px; overflow-y: auto; margin: 12px 0; padding-right: 4px; }
        .music-list-scroll::-webkit-scrollbar { width: 4px; }
        .music-list-scroll::-webkit-scrollbar-thumb { background: var(--theme-accent); border-radius: 4px; }
        .music-list-bar { display: flex; gap: 8px; overflow-x: auto; padding: 8px 0; margin: 8px 0; scrollbar-width: thin; }
        .music-list-bar::-webkit-scrollbar { height: 4px; }
        .music-list-bar::-webkit-scrollbar-thumb { background: var(--theme-accent); border-radius: 4px; }
        .music-list-chip { min-width: 120px; background: var(--theme-input-bg); border-radius: 16px; padding: 10px 14px; text-align: center; border: 0.5px solid var(--theme-border); font-size: 13px; color: var(--theme-status); font-weight: 500; cursor: pointer; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .music-list-chip.active { background: var(--theme-accent); color: white; border-color: var(--theme-accent); }

        /* ===== 占卜系统 - 真实水晶球 ===== */
        .crystal-scene { position: relative; width: 200px; height: 200px; margin: 20px auto; display: flex; align-items: center; justify-content: center; }
        .crystal-ball {
            width: 130px; height: 130px; border-radius: 50%;
            background: 
                radial-gradient(circle at 30% 30%, rgba(255,255,255,0.8) 0%, rgba(233,213,255,0.3) 15%, transparent 40%),
                radial-gradient(circle at 50% 50%, rgba(168,85,247,0.4) 0%, rgba(88,28,135,0.6) 60%, rgba(76,29,149,0.8) 100%);
            box-shadow: 
                0 0 30px rgba(168,85,247,0.4),
                0 0 60px rgba(168,85,247,0.2),
                inset 0 0 20px rgba(255,255,255,0.15),
                inset 0 0 40px rgba(168,85,247,0.2);
            position: relative; cursor: pointer; z-index: 2;
            border: 1px solid rgba(255,255,255,0.3);
            backdrop-filter: blur(2px);
        }
        .crystal-ball::before {
            content: ''; position: absolute; top: 8%; left: 15%; width: 25%; height: 18%;
            border-radius: 50%; background: rgba(255,255,255,0.6); filter: blur(3px); transform: rotate(-25deg);
        }
        .crystal-ball::after {
            content: ''; position: absolute; bottom: -8px; left: 50%; transform: translateX(-50%);
            width: 80px; height: 20px; border-radius: 50%; background: radial-gradient(ellipse, rgba(168,85,247,0.3), transparent);
        }
        .crystal-stand {
            position: absolute; bottom: 25px; width: 70px; height: 22px;
            background: linear-gradient(180deg, #5b21b6, #3b0764); border-radius: 50%; z-index: 1;
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
        }
        .crystal-smoke { position: absolute; width: 200px; height: 200px; top: -30px; left: 0; pointer-events: none; z-index: 3; }
        .smoke-particle {
            position: absolute; border-radius: 50%;
            background: radial-gradient(circle, rgba(192,132,252,0.35), rgba(168,85,247,0.15), transparent);
            filter: blur(10px);
        }
        @keyframes smokeRise {
            0% { transform: translateY(80px) scale(0.5) rotate(0deg); opacity: 0; }
            20% { opacity: 0.6; }
            50% { transform: translateY(30px) scale(1.2) rotate(45deg); opacity: 0.4; }
            100% { transform: translateY(-20px) scale(1.8) rotate(90deg); opacity: 0; }
        }
        
        .fortune-modes { display: flex; gap: 8px; justify-content: center; margin: 12px 0; flex-wrap: wrap; }
        .fortune-mode-btn { background: linear-gradient(135deg, #a855f7, #7c3aed); color: white; border: none; border-radius: 20px; padding: 8px 16px; font-size: 13px; font-weight: 600; cursor: pointer; box-shadow: 0 4px 12px rgba(168,85,247,0.3); }
        .fortune-mode-btn:active { transform: scale(0.95); }
        
        .tarot-area { display: none; margin-top: 12px; }
        .tarot-question { width: 100%; border: 0.5px solid var(--theme-border); border-radius: 20px; padding: 10px; background: var(--theme-input-bg); color: var(--theme-status); margin-bottom: 10px; font-size: 14px; }
        .tarot-table { display: flex; gap: 8px; justify-content: center; flex-wrap: wrap; margin: 12px 0; min-height: 120px; }
        .tarot-card-item {
            width: 70px; height: 110px; border-radius: 10px;
            background: linear-gradient(135deg, #4c1d95, #7c3aed);
            border: 2px solid #a855f7; display: flex; align-items: center; justify-content: center;
            color: white; font-size: 11px; font-weight: 600; cursor: pointer;
            transition: all 0.5s; position: relative; box-shadow: 0 4px 12px rgba(0,0,0,0.2);
            overflow: hidden;
        }
        .tarot-card-item.revealed {
            background: linear-gradient(135deg, #f3e8ff, #e9d5ff);
            border-color: #c084fc; color: #581c87;
        }
        .tarot-card-pattern {
            position: absolute; top: 0; left: 0; right: 0; bottom: 0;
            opacity: 0.15; font-size: 60px; display: flex; align-items: center; justify-content: center;
        }
        .tarot-card-name { position: relative; z-index: 1; font-weight: 700; font-size: 12px; text-align: center; padding: 4px; }
        .tarot-card-mean { position: relative; z-index: 1; font-size: 9px; text-align: center; padding: 2px 4px; opacity: 0.9; }
        
        .stick-area { display: none; margin-top: 12px; }
        .stick-result { background: var(--theme-header); border-radius: 20px; padding: 16px; margin-top: 12px; text-align: center; border: 1px solid var(--theme-border); display: none; }
        .stick-level { font-size: 22px; font-weight: 700; color: var(--theme-accent); margin-bottom: 6px; }
        .stick-poem { font-size: 14px; color: var(--theme-status); line-height: 1.5; margin-bottom: 6px; font-style: italic; }
        .stick-explain { font-size: 12px; color: var(--theme-accent); }
        
        .weekly-fortune { display: none; margin-top: 12px; }
        .weekly-day { background: var(--theme-header); border-radius: 14px; padding: 10px; margin: 6px 0; border: 0.5px solid var(--theme-border); }
        .weekly-day-name { font-weight: 600; color: var(--theme-accent); font-size: 13px; }
        .weekly-day-text { font-size: 12px; color: var(--theme-status); margin-top: 3px; }

        /* ===== 专注模式 - 大圆圈水波纹 ===== */
        .focus-scene {
            position: relative;
            width: 280px;
            height: 280px;
            margin: 10px auto;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .focus-center-circle {
            width: 200px;
            height: 200px;
            border-radius: 50%;
            background: linear-gradient(135deg, var(--theme-accent), #ff6b8a);
            display: flex;
            align-items: center;
            justify-content: center;
            flex-direction: column;
            color: white;
            position: relative;
            z-index: 2;
            box-shadow: 0 8px 32px rgba(255, 138, 159, 0.4);
            transition: all 0.3s;
        }
        .focus-ripple-ring {
            position: absolute;
            border-radius: 50%;
            border: 2.5px solid var(--theme-accent);
            opacity: 0.5;
            animation: focusRipple 2.8s ease-out infinite;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            z-index: 1;
            pointer-events: none;
        }
        @keyframes focusRipple {
            0% { width: 200px; height: 200px; opacity: 0.5; border-width: 2.5px; }
            100% { width: 340px; height: 340px; opacity: 0; border-width: 0.5px; }
        }
        .focus-timer-big {
            font-size: 44px;
            font-weight: 700;
            font-variant-numeric: tabular-nums;
            letter-spacing: 2px;
            text-shadow: 0 2px 8px rgba(0,0,0,0.15);
        }
        .focus-task-display {
            font-size: 14px;
            margin-top: 10px;
            opacity: 0.95;
            font-weight: 500;
            max-width: 160px;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            text-align: center;
            padding: 0 10px;
        }
        .focus-mode-toggle {
            display: flex;
            justify-content: center;
            gap: 12px;
        }
        .focus-mode-btn {
            background: var(--theme-input-bg);
            border: 1px solid var(--theme-border);
            border-radius: 20px;
            padding: 8px 20px;
            font-size: 13px;
            color: var(--theme-status);
            cursor: pointer;
            font-weight: 600;
        }
        .focus-mode-btn.active {
            background: var(--theme-accent);
            color: white;
            border-color: var(--theme-accent);
        }
        
        /* 黑夜模式 */
        .focus-panel.night-mode { background: #0f0f23 !important; }
        .focus-panel.night-mode .feature-title { color: #e9d5ff; }
        .focus-panel.night-mode .focus-center-circle {
            background: linear-gradient(135deg, #7c3aed, #a855f7);
            box-shadow: 0 8px 32px rgba(168, 85, 247, 0.4);
        }
        .focus-panel.night-mode .focus-ripple-ring {
            border-color: #a855f7;
        }
        .focus-panel.night-mode .focus-timer-big { color: white; }
        .focus-panel.night-mode .focus-task-display { color: rgba(255,255,255,0.9); }
        .focus-panel.night-mode .focus-status { color: #c084fc; }
        .focus-panel.night-mode .focus-input { background: #1a1a3e; color: #e9d5ff; border-color: #4c1d95; }
        .focus-panel.night-mode .focus-chat-label { color: #a855f7; }
        .focus-panel.night-mode .focus-mode-btn { background: #1a1a3e; color: #e9d5ff; border-color: #4c1d95; }
        .focus-panel.night-mode .focus-mode-btn.active { background: #7c3aed; color: white; border-color: #7c3aed; }

        /* 设置面板功能网格 */
        .feature-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-top: 8px; }
        .feature-grid-item {
            background: var(--theme-input-bg); border-radius: 20px; padding: 14px 4px;
            text-align: center; cursor: pointer; border: 1px solid var(--theme-border);
            transition: all 0.15s; font-size: 12px; color: var(--theme-status); font-weight: 600;
        }
        .feature-grid-item:active { transform: scale(0.95); background: var(--theme-header); }
        .feature-grid-item .fg-icon { font-size: 24px; display: block; margin-bottom: 4px; }

        /* 专注模式内发消息 */
        .focus-chat-box { margin-top: 14px; border-top: 1px solid var(--theme-border); padding-top: 12px; }
        .focus-chat-label { font-size: 12px; color: var(--theme-accent); font-weight: 600; margin-bottom: 6px; }
        .focus-chat-row { display: flex; gap: 8px; align-items: center; }

        /* ===== 金币特效 ===== */
        .coin-overlay { position: fixed; top: 0; left: 0; right: 0; bottom: 0; z-index: 400; display: none; align-items: center; justify-content: center; background: rgba(0,0,0,0.4); pointer-events: none; }
        .coin-overlay.show { display: flex; }
        .coin-container { text-align: center; animation: coinPop 1.2s ease-out forwards; }
        @keyframes coinPop { 0% { transform: scale(0) rotate(0deg); opacity: 0; } 30% { transform: scale(1.2) rotate(180deg); opacity: 1; } 60% { transform: scale(1) rotate(360deg); opacity: 1; } 100% { transform: scale(0.8) rotate(540deg); opacity: 0; } }
        .coin-emoji { font-size: 80px; display: block; margin-bottom: 12px; filter: drop-shadow(0 4px 8px rgba(255,215,0,0.5)); }
        .coin-text { color: white; font-size: 24px; font-weight: 700; text-shadow: 0 2px 4px rgba(0,0,0,0.3); }
        .coin-from { color: rgba(255,255,255,0.9); font-size: 14px; margin-top: 6px; }
    </style>
</head>
<body>
<div class="wechat" id="wechatApp">
    <div class="status-bar"><span class="time-left" id="statusTime">9:41</span><div class="battery">📶 🔋</div></div>
    <div class="chat-header">
        <div class="back-icon">←</div>
        <div class="avatar-header" id="myAvatarHeader">😊</div>
        <div class="header-info" id="openSettingsBtn">
            <div class="header-name" id="partnerNameDisplay">越宁</div>
            <div class="header-status"><span style="background:var(--theme-accent); width:8px;height:8px;display:inline-block;border-radius:50%;"></span> 在线</div>
        </div>
        <div class="menu-icon" id="menuSettingsBtn">⋯</div>
    </div>

    <div class="chat-main" id="chatMessages"><div class="date-divider" id="dateDivider"></div></div>

    <div class="input-bar" id="inputBar">
        <button class="icon-btn" id="patBtn" title="拍一拍">👋</button>
        <button class="icon-btn" id="emojiBtn" title="表情包">😊</button>
        <button class="icon-btn" id="callBtn" title="语音通话">📞</button>
        <button class="icon-btn" id="moreBtn" title="更多">☰</button>
        <input type="text" class="input-field" id="msgInput" placeholder="输入消息..." autocomplete="off">
        <button class="send-btn" id="sendBtn">发送</button>
        
        <div class="more-menu" id="moreMenu">
            <div class="more-menu-item" id="menuRedPacket"><span>🧧</span><span>发红包</span></div>
            <div class="more-menu-item" id="menuTransfer"><span>💰</span><span>转账</span></div>
            <div class="more-menu-item" id="menuMusic"><span>🎵</span><span>一起听歌</span></div>
        </div>
    </div>

    <div id="quoteIndicator" class="quote-indicator" style="display: none;">
        <span>正在引用...</span>
        <button class="cancel-quote">✕</button>
    </div>

    <div class="tab-bar">
        <div class="tab-item active" data-tab="chat"><span class="tab-icon">💬</span><span>聊天</span></div>
        <div class="tab-item" data-tab="card"><span class="tab-icon">🗂️</span><span>字卡</span></div>
        <div class="tab-item" data-tab="settings"><span class="tab-icon">⚙️</span><span>设置</span></div>
        <div class="tab-item" data-tab="moment"><span class="tab-icon">🌸</span><span>朋友圈</span></div>
        <div class="tab-item" data-tab="couple"><span class="tab-icon">💕</span><span>情侣空间</span></div>
        <div class="tab-item" data-tab="letter"><span class="tab-icon">✉️</span><span>书信</span></div>
    </div>

    <div id="cardPanel" class="card-pane"></div>
    <div id="momentPanel" class="moment-pane"></div>
    <div id="couplePanel" class="couple-pane"></div>
    <div id="letterPanel" class="letter-pane"></div>
    <div id="settingsTabPanel" class="settings-tab-pane">
        <div style="font-weight:700; font-size:16px; text-align:center; margin-bottom:16px; color:var(--theme-status);">✨ 高级功能</div>
        <div class="feature-grid">
            <div class="feature-grid-item" id="openDressBtn2"><span class="fg-icon">🎨</span><div>主题装扮</div></div>
            <div class="feature-grid-item" id="openFortuneBtn2"><span class="fg-icon">🔮</span><div>今日占卜</div></div>
            <div class="feature-grid-item" id="openFocusBtn2"><span class="fg-icon">🎯</span><div>专注模式</div></div>
            <div class="feature-grid-item" id="openPeriodBtn2"><span class="fg-icon">🌙</span><div>经期助手</div></div>
            <div class="feature-grid-item" id="openGameBtn2"><span class="fg-icon">🎮</span><div>情侣游戏</div></div>
            <div class="feature-grid-item" id="openWaterBtn2"><span class="fg-icon">💧</span><div>喝水提醒</div></div>
        </div>
    </div>

    <div id="overlay" class="overlay"></div>
    
    <!-- 设置面板（基础设置） -->
    <div id="settingsPanel" class="settings-panel">
        <div style="font-weight:700; font-size:16px; text-align:center; margin-bottom:16px; color:var(--theme-status);">⚙️ 基础设置</div>
        
        <div class="setting-item"><span>对方头像</span><div><div id="previewPartnerAvatar" style="width:42px;height:42px;border-radius:50%;background:var(--theme-border);display:inline-block;text-align:center;line-height:42px;">❤️</div> <button id="changePartnerAvatarBtn" style="margin-left:12px; background:var(--theme-accent); border:none; border-radius:30px; padding:4px 12px; color:white;">更换</button></div></div>
        <div class="setting-item"><span>对方昵称</span><input id="partnerNicknameInput" placeholder="昵称" style="border:0.5px solid var(--theme-border);border-radius:28px;padding:6px 12px;background:var(--theme-input-bg);color:var(--theme-status);"></div>
        <div class="setting-item"><span>我的头像</span><div><div id="previewMyAvatar" style="width:42px;height:42px;border-radius:50%;background:var(--theme-border);display:inline-block;text-align:center;line-height:42px;">😊</div> <button id="changeMyAvatarBtn" style="margin-left:12px; background:var(--theme-accent); border:none; border-radius:30px; padding:4px 12px; color:white;">更换</button></div></div>
        <div class="setting-item"><span>我的昵称</span><input id="myNicknameInput" placeholder="昵称" style="border:0.5px solid var(--theme-border);border-radius:28px;padding:6px 12px;background:var(--theme-input-bg);color:var(--theme-status);"></div>
        <div class="setting-item"><span>我的余额</span><input type="number" id="myBalanceInput" value="1000" style="border:0.5px solid var(--theme-border);border-radius:28px;padding:6px 12px;background:var(--theme-input-bg);color:var(--theme-status);width:100px;"></div>
        <div class="setting-item"><span>对方余额</span><input type="number" id="partnerBalanceInput" value="1000" style="border:0.5px solid var(--theme-border);border-radius:28px;padding:6px 12px;background:var(--theme-input-bg);color:var(--theme-status);width:100px;"></div>
        <div class="setting-item"><span>👋 拍一拍后缀</span><input id="patSuffixInput" placeholder="例如：肩膀、小脑袋" style="border:0.5px solid var(--theme-border);border-radius:28px;padding:6px 12px;background:var(--theme-input-bg);color:var(--theme-status);width:120px;"></div>
        <div class="setting-item"><span>🌸 朋友圈背景</span><button id="changeMomentBgBtn" style="background:#c29ebf; border:none; border-radius:30px; padding:4px 12px; color:white;">从相册选择</button></div>
        <div class="setting-item"><span>聊天背景</span><button id="changeBgBtn" style="background:#c29ebf; border:none; border-radius:30px; padding:4px 12px; color:white;">从相册选择</button></div>
        <div class="setting-item"><span>📞 通话背景</span><button id="changeCallBgBtn" style="background:#c29ebf; border:none; border-radius:30px; padding:4px 12px; color:white;">从相册选择</button></div>
        <div class="setting-item"><span>⏱️ 聊天节奏（秒）</span><div><label>最短</label><input type="number" id="minDelaySec" step="0.5" min="0" max="10" value="0.8"> <label>最长</label><input type="number" id="maxDelaySec" step="0.5" min="0.5" max="15" value="3"></div></div>
        <div class="setting-item"><span>📨 最多连续回复</span><input type="number" id="maxConsecutive" min="1" max="5" value="2"></div>
        <div class="setting-item"><span>📮 书信回复延迟（小时）</span><div><label>最短</label><input type="number" id="letterMinHour" step="0.5" min="0.5" max="72" value="1"> <label>最长</label><input type="number" id="letterMaxHour" step="0.5" min="1" max="168" value="24"></div></div>
        <div class="setting-item"><span>📸 表情包管理</span><button id="addEmojiBtn" style="background:var(--theme-accent); border:none; border-radius:30px; padding:4px 12px; color:white;">从相册添加</button></div>
        <div id="emojiList" class="emoji-management" style="display: flex; flex-wrap: wrap; gap: 8px; margin-top: 8px;"></div>
        
        <div class="close-settings" id="closeSettingsBtn">完成</div>
    </div>

    <!-- 装扮面板 -->
    <div id="dressPanel" class="feature-panel">
        <div class="feature-title">🎨 主题装扮</div>
        <div style="font-weight:600; margin:8px 0; color:var(--theme-status); font-size: 14px;">主题颜色</div>
        <div class="theme-grid" id="themeGrid"></div>
        <div style="font-weight:600; margin:12px 0 8px; color:var(--theme-status); font-size: 14px;">聊天气泡</div>
        <div class="style-grid" id="styleGrid"></div>
        <div style="font-weight:600; margin:12px 0 8px; color:var(--theme-status); font-size: 14px;">字体</div>
        <div class="font-grid" id="fontGrid"></div>
        <div class="close-feature" id="closeDressBtn">关闭</div>
    </div>

    <!-- 占卜面板（真实水晶球版） -->
    <div id="fortunePanel" class="feature-panel">
        <div class="feature-title">🔮 神秘占卜</div>
        
        <div class="crystal-scene" id="crystalScene">
            <div class="crystal-stand"></div>
            <div class="crystal-ball" id="crystalBall" title="点击水晶球揭示命运"></div>
            <div class="crystal-smoke" id="crystalSmoke"></div>
        </div>
        <div style="text-align:center; color:#a855f7; font-size:13px; margin-bottom:12px; font-weight:500;">点击水晶球，命运将为你显现 ✨</div>
        
        <div class="fortune-modes">
            <button class="fortune-mode-btn" id="modeWeekly">📅 本周运势</button>
            <button class="fortune-mode-btn" id="modeTarot">🃏 塔罗抽牌</button>
            <button class="fortune-mode-btn" id="modeStick">🎋 灵签占卜</button>
        </div>
        
        <!-- 本周运势 -->
        <div class="weekly-fortune" id="weeklyFortune">
            <div style="font-weight:600; color:var(--theme-status); margin-bottom:8px;">📅 本周运势</div>
            <div id="weeklyContent"></div>
        </div>
        
        <!-- 塔罗区域 -->
        <div class="tarot-area" id="tarotArea">
            <input type="text" id="tarotQuestion" class="tarot-question" placeholder="🌙 写下你想问的问题...（爱情/事业/选择）">
            <div style="text-align:center;"><button class="fortune-btn" id="drawTarotBtn" style="width:auto; padding:10px 24px;">🔮 抽取三张牌阵</button></div>
            <div class="tarot-table" id="tarotTable"></div>
            <div id="tarotResult" style="margin-top:12px; display:none;"></div>
        </div>
        
        <!-- 灵签区域 -->
        <div class="stick-area" id="stickArea">
            <input type="text" id="stickQuestion" class="tarot-question" placeholder="🎋 必须先输入想问的事...">
            <div style="text-align:center;"><button class="fortune-btn" id="drawStickBtn" style="width:auto; padding:10px 24px;">🎋 摇签筒</button></div>
            <div class="stick-result" id="stickResult"></div>
        </div>
        
        <div class="close-feature" id="closeFortuneBtn">关闭</div>
    </div>

    <!-- 专注面板（大圆圈水波纹版） -->
    <div id="focusPanel" class="feature-panel focus-panel">
        <div class="feature-title">🎯 专注模式</div>
        
        <!-- 模式切换 -->
        <div class="focus-mode-toggle" style="margin-bottom:20px;">
            <button class="focus-mode-btn active" id="focusDayBtn">☀️ 白天</button>
            <button class="focus-mode-btn" id="focusNightBtn">🌙 黑夜</button>
        </div>
        
        <!-- 情侣信息栏 -->
        <div class="focus-couple-bar">
            <div class="focus-couple-avatar" id="focusMyAvatar"></div>
            <div class="focus-couple-info">
                <div class="focus-couple-name" id="focusMyName">我</div>
            </div>
            <div class="focus-couple-heart">💕</div>
            <div class="focus-couple-info">
                <div class="focus-couple-name" id="focusPartnerName">越宁</div>
            </div>
            <div class="focus-couple-avatar" id="focusPartnerAvatar"></div>
        </div>
        
        <!-- 设置界面 -->
        <div id="focusSetupArea">
            <div style="text-align:center; color:var(--theme-accent); font-size:13px; margin-bottom:12px; font-weight:500;">🍅 默认专注 25 分钟</div>
            <input id="focusTask" class="focus-input" placeholder="输入专注事项（例如：学习、工作）" style="text-align:center; font-size:15px;">
            <div class="focus-btn-group" style="margin-top:20px;">
                <button class="focus-btn focus-start" id="focusStartBtn">开始专注</button>
            </div>
        </div>
        
        <!-- 专注中界面（大圆圈+水波纹） -->
        <div id="focusActiveArea" style="display:none; text-align:center; position:relative; padding:10px 0;">
            <div class="focus-scene">
                <div class="focus-ripple-ring"></div>
                <div class="focus-ripple-ring" style="animation-delay:0.8s;"></div>
                <div class="focus-ripple-ring" style="animation-delay:1.6s;"></div>
                <div class="focus-center-circle" id="focusCenterCircle">
                    <div class="focus-timer-big" id="focusTimerBig">25:00</div>
                    <div class="focus-task-display" id="focusTaskDisplay">学习</div>
                </div>
            </div>
            
            <div class="focus-btn-group" style="margin-top:24px;">
                <button class="focus-btn focus-pause" id="focusPauseBtn" style="display:none;">暂停</button>
                <button class="focus-btn focus-stop" id="focusStopBtn" style="display:none;">放弃</button>
            </div>
            <div class="focus-status" id="focusStatus" style="margin-top:12px;">准备开始专注</div>
        </div>
        
        <!-- 戳一戳和听歌 -->
        <div style="display:flex; justify-content:center; gap:10px; margin-top:14px;">
            <button class="focus-action-btn" id="focusPatBtn" style="background:var(--theme-header); border:1px solid var(--theme-border); border-radius:20px; padding:8px 16px; font-size:13px; color:var(--theme-status); cursor:pointer; font-weight:500;">👋 戳戳他</button>
            <button class="focus-action-btn" id="focusMusicBtn" style="background:var(--theme-header); border:1px solid var(--theme-border); border-radius:20px; padding:8px 16px; font-size:13px; color:var(--theme-status); cursor:pointer; font-weight:500;">🎵 听歌</button>
        </div>
        
        <!-- 专注模式内发消息 -->
        <div class="focus-chat-box" id="focusChatBox" style="display:none;">
            <div id="focusInternalMsgs" style="max-height:100px; overflow-y:auto; margin-bottom:8px; font-size:12px;"></div>
            <div class="focus-chat-label">💬 悄悄发消息给<span id="focusChatLabelName">越宁</span></div>
            <div class="focus-chat-row">
                <input type="text" id="focusMsgInput" class="focus-input" placeholder="输入消息..." style="flex:1; font-size:14px; padding:10px 14px; margin-bottom:0;">
                <button id="focusSendMsgBtn" style="background:var(--theme-accent); color:white; border:none; border-radius:24px; padding:10px 18px; font-weight:600; cursor:pointer; font-size:13px; white-space:nowrap;">发送</button>
            </div>
        </div>
        
        <div class="close-feature" id="closeFocusBtn">关闭</div>
    </div>

    <!-- 经期面板 -->
    <div id="periodPanel" class="feature-panel">
        <div class="feature-title">🌙 经期助手</div>
        <label style="font-size:13px; color:var(--theme-status); font-weight:500;">上次月经开始日期</label>
        <input type="date" id="periodLastDate" class="period-input">
        <label style="font-size:13px; color:var(--theme-status); font-weight:500;">周期长度（天）</label>
        <input type="number" id="periodCycle" class="period-input" value="28" min="21" max="35">
        <button id="savePeriodBtn" class="fortune-btn" style="margin-top:8px;">保存记录</button>
        <div id="periodInfo" class="period-info" style="display:none;">
            <div class="period-phase" id="periodPhase"></div>
            <div class="period-countdown" id="periodCountdown"></div>
        </div>
        <div class="close-feature" id="closePeriodBtn">关闭</div>
    </div>

    <!-- 游戏面板 -->
    <div id="gamePanel" class="feature-panel">
        <div class="feature-title">🎮 情侣游戏</div>
        <div id="gameMenu">
            <div class="game-option" data-game="guess">🤔 猜我在干嘛</div>
            <div class="game-option" data-game="truth">💕 真心话大冒险</div>
            <div class="game-option" data-game="quiz">❓ 爱情问答</div>
        </div>
        <div id="gameArea" style="display:none;">
            <div class="game-question" id="gameQuestion"></div>
            <div id="gameOptions" style="display:flex; flex-direction:column; gap:8px; margin:12px 0;"></div>
            <div class="game-result" id="gameResult" style="display:none;"></div>
            <button class="fortune-btn" id="backGameBtn" style="margin-top:12px;">返回菜单</button>
        </div>
        <div class="close-feature" id="closeGameBtn">关闭</div>
    </div>

    <!-- 喝水面板 -->
    <div id="waterPanel" class="feature-panel">
        <div class="feature-title">💧 喝水提醒</div>
        <div class="water-status" id="waterStatus">今日已喝 0 / 8 杯</div>
        <div class="water-cups" id="waterCups"></div>
        <div class="water-settings">
            <div><label>目标杯数</label><input type="number" id="waterGoal" value="8" min="1" max="20"></div>
            <div><label>提醒间隔(小时)</label><input type="number" id="waterInterval" value="2" min="1" max="8" step="0.5"></div>
        </div>
        <div style="text-align:center; margin-top: 12px;">
            <button class="water-remind-btn" id="drinkBtn">💧 喝了一杯</button>
            <button class="water-remind-btn" id="saveWaterBtn" style="background:var(--theme-status); margin-left:8px;">保存设置</button>
        </div>
        <div class="close-feature" id="closeWaterBtn">关闭</div>
    </div>

    <!-- 音乐面板 -->
    <div id="musicPanel" class="music-panel">
        <div class="feature-title">🎵 音乐播放器</div>
        <div class="music-add-area">
            <input id="musicNameInput" class="music-input" placeholder="歌曲名字">
            <input id="musicUrlInput" class="music-input" placeholder="网易云歌曲链接（可选）或留空选本地MP3">
            <input type="file" id="musicFileInput" accept="audio/mp3,audio/*" style="display:none;">
            <button class="fortune-btn" id="pickMusicFileBtn" style="margin:4px 0;">📁 选择本地MP3</button>
            <button class="fortune-btn" id="addMusicBtn" style="margin-top:4px;">➕ 添加到歌单</button>
        </div>
        <div style="font-weight:600; color:var(--theme-status); font-size:14px; margin:8px 0;">🎶 我的歌单（点击直接播放）</div>
        <div class="music-list-bar" id="musicListBar"></div>
        <div class="music-list-scroll" id="musicListScroll"></div>
        <div style="text-align:center; margin: 16px 0;">
            <button class="fortune-btn" id="togetherListenBtn" style="background: linear-gradient(135deg, #ff6b6b, #feca57);">🎧 邀请越宁一起听</button>
        </div>
        <div class="close-feature" id="closeMusicBtn">关闭</div>
    </div>

    <!-- 一起听界面 -->
    <div id="musicTogetherPanel" class="music-panel" style="z-index:27;">
        <div class="feature-title">🎧 一起听歌</div>
        <div class="music-together">
            <div class="music-together-avatar" id="myMusicAvatar"></div>
            <div style="font-size:24px;">💕</div>
            <div class="music-together-avatar" id="partnerMusicAvatar"></div>
        </div>
        <div style="text-align:center; color:var(--theme-status); font-weight:600; margin:8px 0;" id="togetherSongName">暂无歌曲</div>
        <div class="vinyl-record spinning" id="vinylRecord">
            <div class="vinyl-center" id="vinylCenter">
                <img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='70' height='70'%3E%3Crect fill='%23ff8a9f' width='70' height='70'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='white' font-size='24'%3E🎵%3C/text%3E%3C/svg%3E" id="vinylImg">
            </div>
        </div>
        <div style="text-align:center; margin: 12px 0;">
            <button class="game-btn" id="playPauseBtn">⏸️ 暂停</button>
            <button class="game-btn" id="changeVinylBtn" style="background:#c29ebf;">🖼️ 换唱片封面</button>
            <button class="game-btn" id="minimizeMusicBtn" style="background:var(--theme-status);">📌 挂起</button>
        </div>
        <div class="close-feature" id="closeTogetherBtn">退出一起听</div>
    </div>

    <!-- 通话全屏界面 -->
    <div id="callScreen" class="call-screen">
        <div class="call-screen-bg" id="callScreenBg"></div>
        <div class="call-screen-content">
            <div class="call-screen-avatar" id="callScreenAvatar">❤️</div>
            <div class="call-screen-name" id="callScreenName">越宁</div>
            <div class="call-screen-status" id="callScreenStatus">正在呼叫...</div>
            <div class="call-screen-btns">
                <button class="call-screen-btn call-mute" id="callMuteBtn">🎙️</button>
                <button class="call-screen-btn call-reject" id="callRejectBtn">📞</button>
                <button class="call-screen-btn call-speaker" id="callSpeakerBtn">🔊</button>
            </div>
        </div>
    </div>

    <!-- 红包弹窗 -->
    <div id="redpacketOverlay" class="redpacket-overlay">
        <div class="redpacket-card">
            <button class="redpacket-close" id="redpacketCloseBtn">✕</button>
            <div class="redpacket-top">
                <div class="redpacket-from" id="redpacketFrom">越宁的红包</div>
                <div class="redpacket-bless" id="redpacketBless">恭喜发财，大吉大利</div>
                <button class="redpacket-open-btn" id="redpacketOpenBtn">开</button>
            </div>
            <div class="redpacket-amount" id="redpacketAmount">
                <div class="redpacket-num" id="redpacketNum">0.00</div>
                <div class="redpacket-unit">元</div>
            </div>
        </div>
    </div>

    <!-- 转账弹窗 -->
    <div id="transferOverlay" class="transfer-overlay">
        <div class="transfer-card">
            <div class="transfer-header">转账给 <span id="transferTargetName">越宁</span></div>
            <input type="number" class="transfer-amount-input" id="transferAmountInput" placeholder="0.00">
            <button class="transfer-btn" id="confirmTransferBtn">转账</button>
        </div>
    </div>

    <!-- 金币特效 -->
    <div id="coinOverlay" class="coin-overlay">
        <div class="coin-container">
            <span class="coin-emoji">🪙</span>
            <div class="coin-text" id="coinText">+0.00元</div>
            <div class="coin-from" id="coinFrom">来自越宁</div>
        </div>
    </div>

    <!-- 音乐悬浮窗 -->
    <div class="music-float" id="musicFloatBtn">🎵</div>
    <div class="music-mini-vinyl" id="musicMiniVinyl">
        <div class="music-mini-center" id="musicMiniCenter"><img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='18' height='18'%3E%3Crect fill='%23ff8a9f' width='18' height='18'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='white' font-size='10'%3E🎵%3C/text%3E%3C/svg%3E"></div>
    </div>

    <div id="overlay2" class="overlay" style="z-index:24;"></div>
</div>

<script>
    function generateId() { return Date.now() * 1000 + Math.floor(Math.random() * 1000); }

    const STORAGE = {
        partnerName: 'yn_partnerName', partnerAvatar: 'yn_partnerAvatar', myAvatar: 'yn_myAvatar', myName: 'yn_myName',
        chatBg: 'yn_chatBg', momentBg: 'yn_momentBg', callBg: 'yn_callBg',
        cardGroups: 'yn_cardGroups', moments: 'yn_moments', couple: 'yn_couple', letters: 'yn_letters',
        letterDelay: 'yn_letterDelay', pendingReplies: 'yn_pendingReplies', emojis: 'yn_emojis',
        chatRhythm: 'yn_chatRhythm', maxConsecutive: 'yn_maxConsecutive',
        theme: 'yn_theme', bubbleStyle: 'yn_bubbleStyle', fontStyle: 'yn_fontStyle',
        fortune: 'yn_fortune', focus: 'yn_focus', period: 'yn_period', water: 'yn_water',
        patSuffix: 'yn_patSuffix', musicList: 'yn_musicList', musicVinyl: 'yn_musicVinyl',
        balance: 'yn_balance'
    };
    
    const THEMES = {
        'default': { name: '月白', bg: '#fef6f9', header: '#fff0f3', bubbleUser: '#ffb7c5', bubbleAi: '#ffffff', textUser: '#ffffff', textAi: '#ad5a5a', accent: '#ff8a9f', border: '#ffe0e4', inputBg: '#fff5f7', status: '#ad5a5a' },
        'starPurple': { name: '星紫', bg: '#f3f0ff', header: '#f0ebff', bubbleUser: '#b8a9ff', bubbleAi: '#ffffff', textUser: '#ffffff', textAi: '#5a4cad', accent: '#8b7bff', border: '#e0d8ff', inputBg: '#f8f5ff', status: '#6b5aad' },
        'grapePurple': { name: '葡萄紫', bg: '#f5f0f7', header: '#f0e8f5', bubbleUser: '#c084fc', bubbleAi: '#ffffff', textUser: '#ffffff', textAi: '#5a3d7a', accent: '#a855f7', border: '#e8d5f5', inputBg: '#faf5ff', status: '#6b3d8a' },
        'appleGreen': { name: '青苹果绿', bg: '#f0fff4', header: '#e6ffed', bubbleUser: '#86efac', bubbleAi: '#ffffff', textUser: '#14532d', textAi: '#166534', accent: '#22c55e', border: '#d1fae5', inputBg: '#f0fdf4', status: '#15803d' },
        'grassGreen': { name: '草绿色', bg: '#f4f9f0', header: '#edf5e6', bubbleUser: '#a3c940', bubbleAi: '#ffffff', textUser: '#ffffff', textAi: '#3d5a1a', accent: '#84a938', border: '#dcedc8', inputBg: '#f5f9f0', status: '#4a6b1f' },
        'creamPink': { name: '奶油粉', bg: '#fff5f5', header: '#ffeeee', bubbleUser: '#fda4af', bubbleAi: '#ffffff', textUser: '#ffffff', textAi: '#9f1239', accent: '#fb7185', border: '#ffd1d9', inputBg: '#fff5f7', status: '#be123c' },
        'creamYellow': { name: '奶油黄', bg: '#fffdf5', header: '#fff9e6', bubbleUser: '#fcd34d', bubbleAi: '#ffffff', textUser: '#78350f', textAi: '#92400e', accent: '#f59e0b', border: '#fef3c7', inputBg: '#fffbeb', status: '#b45309' },
        'creamGreen': { name: '奶油绿', bg: '#f5fff8', header: '#eafff0', bubbleUser: '#6ee7b7', bubbleAi: '#ffffff', textUser: '#064e3b', textAi: '#065f46', accent: '#10b981', border: '#d1fae5', inputBg: '#ecfdf5', status: '#047857' },
        'white': { name: '白色', bg: '#f9fafb', header: '#f3f4f6', bubbleUser: '#9ca3af', bubbleAi: '#ffffff', textUser: '#ffffff', textAi: '#374151', accent: '#6b7280', border: '#e5e7eb', inputBg: '#f9fafb', status: '#4b5563' },
        'sakuraPink': { name: '樱花粉', bg: '#fff0f3', header: '#ffe4ea', bubbleUser: '#ff9eb2', bubbleAi: '#ffffff', textUser: '#ffffff', textAi: '#9d174d', accent: '#f43f5e', border: '#fecdd3', inputBg: '#fff1f2', status: '#be123c' }
    };
    const BUBBLE_STYLES = [{ key: 'default', name: '默认' }, { key: 'square', name: '方形' }, { key: 'gradient', name: '渐变' }, { key: 'outline', name: '描边' }, { key: 'shadow', name: '阴影' }, { key: 'heart', name: '爱心' }];
    const FONT_STYLES = [{ key: 'default', name: '系统默认' }, { key: 'handwriting', name: '手写体' }, { key: 'cute', name: '可爱体' }, { key: 'kaiti', name: '楷体' }];

    let partnerData = { name: localStorage.getItem(STORAGE.partnerName) || "越宁", avatar: localStorage.getItem(STORAGE.partnerAvatar) || "❤️" };
    let myData = { avatar: localStorage.getItem(STORAGE.myAvatar) || "😊", name: localStorage.getItem(STORAGE.myName) || "我" };
    let chatBackground = localStorage.getItem(STORAGE.chatBg) || "";
    let momentBackground = localStorage.getItem(STORAGE.momentBg) || "";
    let callBackground = localStorage.getItem(STORAGE.callBg) || "";
    let cardGroups = JSON.parse(localStorage.getItem(STORAGE.cardGroups) || '[{"name":"日常可爱","cards":["今天天气不错","你吃饭了吗","在干嘛呢","想你了"]},{"name":"关心呵护","cards":["按时吃饭","多穿点","记得喝水","路上小心","好好吃饭","别着凉","注意休息","安全第一"]}]');
    let moments = JSON.parse(localStorage.getItem(STORAGE.moments) || '[]');
    let couple = JSON.parse(localStorage.getItem(STORAGE.couple) || '{"anniversary":"2024-01-01","messages":[],"wallpaper":""}');
    let letters = JSON.parse(localStorage.getItem(STORAGE.letters) || '[]');
    let pendingReplies = JSON.parse(localStorage.getItem(STORAGE.pendingReplies) || '[]');
    let emojis = JSON.parse(localStorage.getItem(STORAGE.emojis) || '[]');
    let chatRhythm = JSON.parse(localStorage.getItem(STORAGE.chatRhythm) || '{"minDelay":0.8,"maxDelay":3}');
    let maxConsecutive = parseInt(localStorage.getItem(STORAGE.maxConsecutive) || '2');
    let letterDelay = JSON.parse(localStorage.getItem(STORAGE.letterDelay) || '{"minHours":1,"maxHours":24}');
    let currentTheme = localStorage.getItem(STORAGE.theme) || 'default';
    let currentBubbleStyle = localStorage.getItem(STORAGE.bubbleStyle) || 'default';
    let currentFontStyle = localStorage.getItem(STORAGE.fontStyle) || 'default';
    let patSuffix = localStorage.getItem(STORAGE.patSuffix) || '肩膀';
    let musicList = JSON.parse(localStorage.getItem(STORAGE.musicList) || '[]');
    let musicVinylImg = localStorage.getItem(STORAGE.musicVinyl) || '';
    let myBalance = parseFloat(localStorage.getItem(STORAGE.balance + '_my') || '1000');
    let partnerBalance = parseFloat(localStorage.getItem(STORAGE.balance + '_partner') || '1000');
    
    let isFocusing = false;
    let focusTimerInterval = null;
    let focusSeconds = 25 * 60;
    let focusTotal = 25 * 60;
    let focusPaused = false;
    let messages = [];
    let callFloat = null, callInterval = null, callSeconds = 0;
    let lastReplyMap = new Map();
    let typingTimeout = null;
    let currentQuoteMsgId = null;
    let replyCheckInterval = null;
    let activePatTimer = null;
    let partnerMomentTimer = null;
    let partnerCommentTimer = null;
    let currentAudio = null;
    let isPlayingMusic = false;
    let currentMusicIndex = -1;
    let isTogetherListening = false;
    let focusModeNight = false;

    function compressImage(file, maxWidth, quality, callback) {
        let reader = new FileReader();
        reader.onload = function(e) {
            let img = new Image();
            img.onload = function() {
                let canvas = document.createElement('canvas');
                let w = img.width, h = img.height;
                if (w > maxWidth) { h = Math.round(h * (maxWidth / w)); w = maxWidth; }
                canvas.width = w; canvas.height = h;
                let ctx = canvas.getContext('2d');
                ctx.drawImage(img, 0, 0, w, h);
                callback(canvas.toDataURL('image/jpeg', quality));
            };
            img.src = e.target.result;
        };
        reader.readAsDataURL(file);
    }

    function getAllCards() { let all = []; for(let g of cardGroups) all.push(...g.cards); return all; }
    function getSmartReply(forWho, input) {
        let cards = getAllCards();
        if(cards.length === 0) return "嗯嗯";
        let last = lastReplyMap.get(forWho) || '';
        let available = cards.filter(c => c !== last);
        if(available.length === 0) available = cards;
        let selected = available[Math.floor(Math.random() * available.length)];
        lastReplyMap.set(forWho, selected);
        return selected;
    }
    function formatMsgTime(date) { return `${date.getHours().toString().padStart(2,'0')}:${date.getMinutes().toString().padStart(2,'0')}`; }
    function escapeHtml(str) { if(!str) return ''; return str.replace(/[&<>]/g, m => ({'&':'&amp;','<<':'&lt;','>':'&gt;'}[m])); }

    function applyTheme(themeKey) {
        const t = THEMES[themeKey] || THEMES['default'];
        const root = document.documentElement;
        root.style.setProperty('--theme-bg', t.bg); root.style.setProperty('--theme-header', t.header);
        root.style.setProperty('--theme-bubble-user', t.bubbleUser); root.style.setProperty('--theme-bubble-ai', t.bubbleAi);
        root.style.setProperty('--theme-text-user', t.textUser); root.style.setProperty('--theme-text-ai', t.textAi);
        root.style.setProperty('--theme-accent', t.accent); root.style.setProperty('--theme-border', t.border);
        root.style.setProperty('--theme-input-bg', t.inputBg); root.style.setProperty('--theme-status', t.status);
        root.style.setProperty('--theme-card-bg', '#ffffff');
    }
    function applyBubbleStyle(styleKey) {
        const app = document.getElementById('wechatApp');
        BUBBLE_STYLES.forEach(s => app.classList.remove('bubble-style-' + s.key));
        if(styleKey && styleKey !== 'default') app.classList.add('bubble-style-' + styleKey);
    }
    function applyFontStyle(fontKey) {
        const app = document.getElementById('wechatApp');
        FONT_STYLES.forEach(s => app.classList.remove('font-' + s.key));
        if(fontKey && fontKey !== 'default') app.classList.add('font-' + fontKey);
    }

    function renderMessages() {
        let container = document.getElementById('chatMessages');
        if(!container) return;
        let dateDiv = container.querySelector('.date-divider');
        container.innerHTML = ''; 
        if(dateDiv) container.appendChild(dateDiv);
        messages.forEach(msg => {
            if(msg.type === 'pat') {
                let sysDiv = document.createElement('div');
                sysDiv.className = 'system-message';
                sysDiv.innerText = msg.content;
                container.appendChild(sysDiv);
                return;
            }
            if(msg.isRevoked) {
                let revokeDiv = document.createElement('div');
                revokeDiv.className = 'system-message';
                revokeDiv.innerText = msg.revokeText || (msg.fromUser ? '你撤回了一条消息' : `“${partnerData.name}”撤回了一条消息`);
                container.appendChild(revokeDiv);
                return;
            }
            let row = document.createElement('div'); 
            row.className = `msg-row ${msg.fromUser ? 'user' : 'ai'}`;
            let avatarDiv = document.createElement('div'); 
            avatarDiv.className = 'avatar';
            if(msg.fromUser) {
                if(myData.avatar.startsWith('data:image')) avatarDiv.innerHTML = `<img src="${myData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`;
                else avatarDiv.innerText = myData.avatar;
            } else {
                if(partnerData.avatar.startsWith('data:image')) avatarDiv.innerHTML = `<img src="${partnerData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`;
                else avatarDiv.innerText = partnerData.avatar;
            }
            
            let quoteHtml = '';
            if(msg.quoteMsgId) {
                let quotedMsg = messages.find(m => m.id === msg.quoteMsgId);
                if(quotedMsg && !quotedMsg.isRevoked) {
                    let quotedSender = quotedMsg.fromUser ? myData.name : partnerData.name;
                    let quotedContent = quotedMsg.type === 'text' ? quotedMsg.content : (quotedMsg.type === 'image' ? '[图片]' : (quotedMsg.type === 'call' ? '[通话]' : (quotedMsg.type === 'redEnvelope' ? '[红包]' : (quotedMsg.type === 'transfer' ? '[转账]' : '[消息]'))));
                    quoteHtml = `<div class="quote-block"><div class="quote-sender">${escapeHtml(quotedSender)}</div>${escapeHtml(quotedContent)}</div>`;
                }
            }
            
            let bubbleDiv = document.createElement('div');
            bubbleDiv.className = 'bubble';
            bubbleDiv.setAttribute('data-id', msg.id);
            bubbleDiv.setAttribute('data-from', msg.fromUser);
            
            if(msg.type === 'text') {
                bubbleDiv.innerHTML = `${quoteHtml}${escapeHtml(msg.content)}`;
            } else if(msg.type === 'image') {
                bubbleDiv.innerHTML = `${quoteHtml}<div class="image-msg"><img src="${msg.content}" alt="表情包"></div>`;
            } else if(msg.type === 'call') {
                bubbleDiv.innerHTML = `${quoteHtml}📞 ${escapeHtml(msg.content)}`;
            } else if(msg.type === 'redEnvelope') {
                bubbleDiv.className = 'bubble red-bubble';
                let statusText = msg.opened ? '已领取' : '微信红包';
                bubbleDiv.innerHTML = `<div class="red-icon">🧧</div><div><div class="red-text">${escapeHtml(msg.bless || '恭喜发财，大吉大利')}</div><div class="red-sub">${statusText}</div></div>`;
                bubbleDiv.onclick = () => openRedPacket(msg);
            } else if(msg.type === 'transfer') {
                bubbleDiv.className = 'bubble transfer-bubble';
                let statusText = msg.opened ? '已收款' : '转账';
                bubbleDiv.innerHTML = `<div class="red-icon">💰</div><div><div class="red-text">${msg.amount}元</div><div class="red-sub">${statusText}</div></div>`;
                bubbleDiv.onclick = () => openTransfer(msg);
            } else {
                bubbleDiv.innerHTML = `${quoteHtml}${escapeHtml(msg.content)}`;
            }
            
            let contentDiv = document.createElement('div');
            contentDiv.appendChild(bubbleDiv);
            let timeSpan = document.createElement('div'); 
            timeSpan.className = 'msg-time'; 
            timeSpan.innerText = formatMsgTime(msg.time);
            contentDiv.appendChild(timeSpan);
            row.appendChild(avatarDiv); 
            row.appendChild(contentDiv);
            container.appendChild(row);
        });
        attachMessageContextMenu();
        container.scrollTop = container.scrollHeight;
    }

    function attachMessageContextMenu() {
        document.querySelectorAll('.bubble').forEach(bubble => {
            bubble.addEventListener('contextmenu', onBubbleContextMenu);
            let pressTimer;
            bubble.addEventListener('touchstart', (e) => {
                pressTimer = setTimeout(() => { onBubbleContextMenu(e); }, 600);
            }, {passive: true});
            bubble.addEventListener('touchend', () => clearTimeout(pressTimer));
            bubble.addEventListener('touchmove', () => clearTimeout(pressTimer));
        });
    }
    
    function onBubbleContextMenu(e) {
        e.preventDefault();
        let bubble = e.target.closest('.bubble');
        if(!bubble) return;
        let msgId = parseInt(bubble.getAttribute('data-id'));
        let fromUser = bubble.getAttribute('data-from') === 'true';
        let existingMenu = document.querySelector('.context-menu');
        if(existingMenu) existingMenu.remove();
        
        let menu = document.createElement('div');
        menu.className = 'context-menu';
        let menuHtml = `<div id="quoteMsg">↩️ 引用</div>`;
        if(fromUser) menuHtml += `<div id="revokeMsg">↩️ 撤回</div>`;
        menu.innerHTML = menuHtml;
        document.body.appendChild(menu);
        
        let x = e.clientX || (e.touches && e.touches[0] ? e.touches[0].clientX : 0);
        let y = e.clientY || (e.touches && e.touches[0] ? e.touches[0].clientY : 0);
        if(x === 0 && e.target) {
            let rect = e.target.getBoundingClientRect();
            x = rect.left + rect.width / 2;
            y = rect.top;
        }
        menu.style.left = Math.min(x, window.innerWidth - 150) + 'px';
        menu.style.top = Math.min(y, window.innerHeight - 100) + 'px';
        
        document.getElementById('quoteMsg').onclick = () => {
            currentQuoteMsgId = msgId;
            let quoteDiv = document.getElementById('quoteIndicator');
            let msg = messages.find(m => m.id === msgId);
            if(msg) {
                let sender = msg.fromUser ? myData.name : partnerData.name;
                let preview = msg.type === 'text' ? msg.content.substring(0,35) : (msg.type === 'image' ? '[图片]' : (msg.type === 'call' ? '[通话]' : (msg.type === 'redEnvelope' ? '[红包]' : (msg.type === 'transfer' ? '[转账]' : '[消息]'))));
                quoteDiv.innerHTML = `<span>↩️ 引用 ${escapeHtml(sender)}: ${escapeHtml(preview)}${msg.content && msg.content.length > 35 ? '...' : ''}</span><button class="cancel-quote">✕</button>`;
            }
            quoteDiv.style.display = 'flex';
            document.querySelector('.cancel-quote').onclick = () => { 
                currentQuoteMsgId = null; 
                quoteDiv.style.display = 'none'; 
            };
            menu.remove();
        };
        
        if(fromUser) {
            document.getElementById('revokeMsg').onclick = () => {
                let msg = messages.find(m => m.id === msgId);
                if(msg && !msg.isRevoked) {
                    msg.isRevoked = true;
                    msg.revokeText = '你撤回了一条消息';
                    renderMessages();
                    if(Math.random() < 0.4) {
                        setTimeout(() => { addMessage('text', '怎么撤回了？', false); }, 1200);
                    }
                }
                menu.remove();
            };
        }
        setTimeout(() => { 
            document.addEventListener('click', function rm() { 
                if(menu.parentNode) menu.remove(); 
                document.removeEventListener('click', rm); 
            }); 
        }, 100);
    }

    function addMessage(type, content, fromUser, extra = {}) {
        let msg = { id: generateId(), type, content, fromUser, time: new Date(), isRevoked: false, ...extra };
        if(currentQuoteMsgId && fromUser === true) {
            msg.quoteMsgId = currentQuoteMsgId;
            currentQuoteMsgId = null;
            document.getElementById('quoteIndicator').style.display = 'none';
        }
        messages.push(msg);
        if(messages.length > 50) messages.shift();
        renderMessages();
        return msg;
    }

    function showTyping() {
        let container = document.getElementById('chatMessages');
        if(!container) return;
        let existing = document.getElementById('typingIndicator');
        if(existing) existing.remove();
        let typingDiv = document.createElement('div');
        typingDiv.id = 'typingIndicator';
        typingDiv.className = 'typing-indicator';
        let avatarHtml = partnerData.avatar.startsWith('data:image') ? `<img src="${partnerData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : partnerData.avatar;
        typingDiv.innerHTML = `
            <div class="typing-avatar">${avatarHtml}</div>
            <div class="typing-dots"><div class="typing-dot"></div><div class="typing-dot"></div><div class="typing-dot"></div></div>
            <span style="font-size:12px;">${escapeHtml(partnerData.name)} 正在输入...</span>
        `;
        container.appendChild(typingDiv);
        container.scrollTop = container.scrollHeight;
    }
    function hideTyping() {
        let typing = document.getElementById('typingIndicator');
        if(typing) typing.remove();
    }

    function partnerReplyWithRhythm(userText) {
        if(isFocusing) {
            if(Math.random() < 0.3) addMessage('text', '加油哦，专注完了再聊~', false);
            return;
        }
        if(typingTimeout) clearTimeout(typingTimeout);
        showTyping();
        let delay = Math.random() * (chatRhythm.maxDelay - chatRhythm.minDelay) + chatRhythm.minDelay;
        typingTimeout = setTimeout(() => {
            hideTyping();
            let replyCount = Math.floor(Math.random() * maxConsecutive) + 1;
            for(let i=0; i<<replyCount; i++) {
                setTimeout(() => {
                    let reply = getSmartReply('yuenin', userText);
                    addMessage('text', reply, false);
                    if(Math.random() < 0.05 && messages.length > 0) {
                        let lastMsg = messages[messages.length-1];
                        if(lastMsg && !lastMsg.fromUser && !lastMsg.isRevoked) {
                            lastMsg.isRevoked = true;
                            lastMsg.revokeText = `“${partnerData.name}”撤回了一条消息`;
                            renderMessages();
                        }
                    }
                }, i * 800);
            }
        }, delay * 1000);
    }

    function sendUserMessage(text) {
        if(!text.trim()) return;
        addMessage('text', text, true);
        partnerReplyWithRhythm(text);
    }

    function sendImageMessage(imgUrl) {
        addMessage('image', imgUrl, true);
        partnerReplyWithRhythm('发送了表情包');
    }

    function sendPat(fromUser = true) {
        if(fromUser) {
            addMessage('pat', `你拍了拍"${partnerData.name}"的${patSuffix}`, true);
            partnerReplyWithRhythm('拍了拍');
        } else {
            addMessage('pat', `“${partnerData.name}”拍了拍你的${patSuffix}`, false);
        }
    }

    function scheduleActivePat() {
        if(activePatTimer) clearTimeout(activePatTimer);
        let delay = Math.floor(Math.random() * (45000 - 25000 + 1) + 25000);
        activePatTimer = setTimeout(() => {
            if(document.hidden) { scheduleActivePat(); return; }
            sendPat(false);
            scheduleActivePat();
        }, delay);
    }

    function startCall() {
        let screen = document.getElementById('callScreen');
        let bg = document.getElementById('callScreenBg');
        let avatar = document.getElementById('callScreenAvatar');
        let name = document.getElementById('callScreenName');
        let status = document.getElementById('callScreenStatus');
        
        if(callBackground) bg.style.backgroundImage = `url(${callBackground})`;
        else bg.style.backgroundImage = 'none';
        
        let avatarHtml = partnerData.avatar.startsWith('data:image') ? `<img src="${partnerData.avatar}" style="width:100%;height:100%;object-fit:cover;">` : partnerData.avatar;
        avatar.innerHTML = avatarHtml;
        name.innerText = partnerData.name;
        status.innerText = '正在呼叫...';
        screen.classList.add('show');
        
        setTimeout(() => {
            status.innerText = '正在通话...';
            addMessage('call', '通话已开始', false);
            screen.classList.remove('show');
            createCallFloat();
        }, 3000);
        
        document.getElementById('callRejectBtn').onclick = () => {
            screen.classList.remove('show');
            addMessage('call', '通话已取消', true);
        };
        document.getElementById('callMuteBtn').onclick = function() {
            this.style.background = this.style.background === 'rgb(34, 197, 94)' ? 'rgba(255,255,255,0.2)' : '#22c55e';
        };
        document.getElementById('callSpeakerBtn').onclick = function() {
            this.style.background = this.style.background === 'rgb(34, 197, 94)' ? 'rgba(255,255,255,0.2)' : '#22c55e';
        };
    }

    function createCallFloat() {
        if(callFloat) callFloat.remove();
        callFloat = document.createElement('div');
        callFloat.className = 'call-float large';
        callFloat.style.top = '100px';
        callFloat.style.right = '10px';
        let avatarHtml = partnerData.avatar.startsWith('data:image') ? `<img src="${partnerData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : partnerData.avatar;
        callFloat.innerHTML = `
            <div class="call-avatar-mini">${avatarHtml}</div>
            <div class="call-info"><div>${escapeHtml(partnerData.name)}</div><div id="callTimer">00:00</div></div>
            <button class="call-hangup-float" id="floatHangupBtn">挂断</button>
        `;
        document.body.appendChild(callFloat);
        let offsetX, offsetY, isDrag = false;
        callFloat.addEventListener('mousedown', (e) => { isDrag=true; offsetX=e.clientX-callFloat.offsetLeft; offsetY=e.clientY-callFloat.offsetTop; });
        window.addEventListener('mousemove', (e) => { if(isDrag) { callFloat.style.left = (e.clientX-offsetX)+'px'; callFloat.style.top = (e.clientY-offsetY)+'px'; callFloat.style.right = 'auto'; } });
        window.addEventListener('mouseup', () => isDrag=false);
        let sizeMenu = document.createElement('div');
        sizeMenu.style.position='absolute'; sizeMenu.style.bottom='-40px'; sizeMenu.style.left='0';
        sizeMenu.style.background='#333'; sizeMenu.style.borderRadius='20px'; sizeMenu.style.display='flex';
        sizeMenu.style.gap='4px'; sizeMenu.style.padding='4px';
        let btnLarge = document.createElement('button'); btnLarge.innerText = '大'; btnLarge.style.cssText = 'background:#555; border:none; color:white; border-radius:50%; width:24px; cursor:pointer;';
        btnLarge.onclick = (e) => { e.stopPropagation(); callFloat.className = 'call-float large'; };
        let btnMedium = document.createElement('button'); btnMedium.innerText = '中'; btnMedium.style.cssText = 'background:#555; border:none; color:white; border-radius:50%; width:24px; cursor:pointer;';
        btnMedium.onclick = (e) => { e.stopPropagation(); callFloat.className = 'call-float medium'; };
        sizeMenu.appendChild(btnLarge); sizeMenu.appendChild(btnMedium);
        callFloat.appendChild(sizeMenu);
        document.getElementById('floatHangupBtn').onclick = () => {
            if(callInterval) clearInterval(callInterval);
            callFloat.remove(); callFloat = null;
            addMessage('call', '通话已结束', false);
        };
        callSeconds = 0;
        callInterval = setInterval(() => {
            callSeconds++;
            let mins = Math.floor(callSeconds/60), secs = callSeconds%60;
            let timerSpan = document.getElementById('callTimer');
            if(timerSpan) timerSpan.innerText = `${mins}:${secs.toString().padStart(2,'0')}`;
        }, 1000);
    }

    function showCoinEffect(amount, fromName) {
        let overlay = document.getElementById('coinOverlay');
        document.getElementById('coinText').innerText = `+${parseFloat(amount).toFixed(2)}元`;
        document.getElementById('coinFrom').innerText = `来自 ${fromName}`;
        overlay.classList.add('show');
        setTimeout(() => overlay.classList.remove('show'), 1200);
    }

    function sendRedPacket() {
        let bless = prompt('输入祝福语：', '恭喜发财，大吉大利');
        if(!bless) bless = '恭喜发财，大吉大利';
        let amount = prompt('输入金额（元）：', '5.20');
        if(!amount || isNaN(amount)) return;
        amount = parseFloat(amount);
        if(amount > myBalance) { alert('余额不足！当前余额：' + myBalance.toFixed(2)); return; }
        myBalance -= amount;
        localStorage.setItem(STORAGE.balance + '_my', myBalance);
        addMessage('redEnvelope', '', true, { bless, amount, opened: false });
    }

    function openRedPacket(msg) {
        if(msg.fromUser) { alert('这是你自己发的红包哦~'); return; }
        if(msg.opened) { alert('红包已领取'); return; }
        let overlay = document.getElementById('redpacketOverlay');
        let fromName = msg.fromUser ? myData.name : partnerData.name;
        document.getElementById('redpacketFrom').innerText = `${fromName}的红包`;
        document.getElementById('redpacketBless').innerText = msg.bless || '恭喜发财，大吉大利';
        document.getElementById('redpacketAmount').classList.remove('show');
        let openBtn = document.getElementById('redpacketOpenBtn');
        openBtn.style.display = 'block';
        openBtn.classList.remove('spinning');
        overlay.classList.add('show');
        
        openBtn.onclick = () => {
            openBtn.classList.add('spinning');
            setTimeout(() => {
                msg.opened = true;
                myBalance += msg.amount;
                localStorage.setItem(STORAGE.balance + '_my', myBalance);
                showCoinEffect(msg.amount, fromName);
                document.getElementById('redpacketNum').innerText = msg.amount.toFixed(2);
                document.getElementById('redpacketAmount').classList.add('show');
                openBtn.style.display = 'none';
                renderMessages();
                addMessage('text', `🧧 已领取${fromName}的红包 ${msg.amount.toFixed(2)}元`, true);
            }, 600);
        };
        document.getElementById('redpacketCloseBtn').onclick = () => overlay.classList.remove('show');
    }

    function sendTransfer() {
        document.getElementById('transferTargetName').innerText = partnerData.name;
        document.getElementById('transferOverlay').classList.add('show');
    }
    function openTransfer(msg) {
        if(msg.opened) { alert('转账已领取'); return; }
        if(msg.fromUser) { alert('这是你自己转的账哦~'); return; }
        let fromName = msg.fromUser ? myData.name : partnerData.name;
        if(confirm(`领取 ${fromName} 的转账 ${msg.amount}元？`)) {
            msg.opened = true;
            myBalance += msg.amount;
            localStorage.setItem(STORAGE.balance + '_my', myBalance);
            showCoinEffect(msg.amount, fromName);
            addMessage('text', `💰 已领取${fromName}的转账 ${msg.amount.toFixed(2)}元`, true);
            renderMessages();
        }
    }

    function renderMusicList() {
        let bar = document.getElementById('musicListBar');
        let scroll = document.getElementById('musicListScroll');
        bar.innerHTML = '';
        scroll.innerHTML = '';
        
        musicList.forEach((song, idx) => {
            let chip = document.createElement('div');
            chip.className = 'music-list-chip' + (idx === currentMusicIndex ? ' active' : '');
            chip.innerText = song.name;
            chip.onclick = () => {
                currentMusicIndex = idx;
                renderMusicList();
                playMusic(idx);
            };
            bar.appendChild(chip);
            
            let item = document.createElement('div');
            item.className = 'music-song-item';
            item.innerHTML = `
                <div class="music-song-info">
                    <div class="music-song-name">${escapeHtml(song.name)}</div>
                    <div class="music-song-source">${song.isLocal ? '📁 本地' : '☁️ 网易云'}</div>
                </div>
                <button class="music-play-btn" data-idx="${idx}">▶️</button>
                <button class="music-delete-btn" data-idx="${idx}">🗑️</button>
            `;
            scroll.appendChild(item);
        });
        
        document.querySelectorAll('.music-play-btn').forEach(btn => {
            btn.onclick = () => playMusic(parseInt(btn.dataset.idx));
        });
        document.querySelectorAll('.music-delete-btn').forEach(btn => {
            btn.onclick = () => {
                musicList.splice(parseInt(btn.dataset.idx), 1);
                localStorage.setItem(STORAGE.musicList, JSON.stringify(musicList));
                renderMusicList();
            };
        });
    }

    function playMusic(idx) {
        if(idx < 0 || idx >= musicList.length) return;
        currentMusicIndex = idx;
        let song = musicList[idx];
        document.getElementById('togetherSongName').innerText = song.name;
        
        if(currentAudio) { currentAudio.pause(); currentAudio = null; }
        
        if(song.isLocal && song.data) {
            currentAudio = new Audio(song.data);
        } else if(song.url) {
            currentAudio = new Audio(song.url);
        }
        
        if(currentAudio) {
            currentAudio.play().catch(e => console.log('播放失败', e));
            isPlayingMusic = true;
            updateMusicUI(true);
        }
        renderMusicList();
    }

    function updateMusicUI(playing) {
        let vinyl = document.getElementById('vinylRecord');
        let mini = document.getElementById('musicMiniVinyl');
        let btn = document.getElementById('playPauseBtn');
        if(playing) {
            vinyl.classList.add('spinning');
            mini.classList.add('spinning');
            btn.innerText = '⏸️ 暂停';
        } else {
            vinyl.classList.remove('spinning');
            mini.classList.remove('spinning');
            btn.innerText = '▶️ 播放';
        }
    }

    function initMusic() {
        renderMusicList();
        
        document.getElementById('musicFloatBtn').onclick = () => {
            document.getElementById('musicPanel').classList.add('open');
            document.getElementById('overlay2').classList.add('show');
        };
        document.getElementById('closeMusicBtn').onclick = () => {
            document.getElementById('musicPanel').classList.remove('open');
            document.getElementById('overlay2').classList.remove('show');
        };
        
        let fileInput = document.getElementById('musicFileInput');
        document.getElementById('pickMusicFileBtn').onclick = () => fileInput.click();
        fileInput.onchange = (e) => {
            let file = e.target.files[0];
            if(file) {
                let reader = new FileReader();
                reader.onload = (ev) => {
                    let name = document.getElementById('musicNameInput').value.trim() || file.name.replace(/\.[^/.]+$/, "");
                    musicList.push({ name, isLocal: true, data: ev.target.result, url: '' });
                    localStorage.setItem(STORAGE.musicList, JSON.stringify(musicList));
                    renderMusicList();
                    document.getElementById('musicNameInput').value = '';
                };
                reader.readAsDataURL(file);
            }
        };
        
        document.getElementById('addMusicBtn').onclick = () => {
            let name = document.getElementById('musicNameInput').value.trim();
            let url = document.getElementById('musicUrlInput').value.trim();
            if(!name) { alert('请输入歌曲名'); return; }
            musicList.push({ name, isLocal: false, data: '', url });
            localStorage.setItem(STORAGE.musicList, JSON.stringify(musicList));
            renderMusicList();
            document.getElementById('musicNameInput').value = '';
            document.getElementById('musicUrlInput').value = '';
        };
        
        document.getElementById('togetherListenBtn').onclick = () => {
            if(musicList.length === 0) { alert('请先添加歌曲'); return; }
            document.getElementById('musicPanel').classList.remove('open');
            document.getElementById('musicTogetherPanel').classList.add('open');
            document.getElementById('overlay2').classList.add('show');
            
            let myAv = document.getElementById('myMusicAvatar');
            let ptAv = document.getElementById('partnerMusicAvatar');
            myAv.innerHTML = myData.avatar.startsWith('data:image') ? `<img src="${myData.avatar}">` : myData.avatar;
            ptAv.innerHTML = partnerData.avatar.startsWith('data:image') ? `<img src="${partnerData.avatar}">` : partnerData.avatar;
            
            isTogetherListening = true;
            playMusic(0);
            addMessage('text', `🎧 邀请你一起听《${musicList[0].name}》`, true);
            setTimeout(() => addMessage('text', `好呀！一起听~`, false), 1500);
        };
        
        document.getElementById('playPauseBtn').onclick = () => {
            if(!currentAudio) return;
            if(isPlayingMusic) { currentAudio.pause(); isPlayingMusic = false; }
            else { currentAudio.play(); isPlayingMusic = true; }
            updateMusicUI(isPlayingMusic);
        };
        
        document.getElementById('changeVinylBtn').onclick = () => {
            let inp = document.createElement('input');
            inp.type = 'file'; inp.accept = 'image/*';
            inp.onchange = (e) => {
                let f = e.target.files[0];
                if(f) {
                    let r = new FileReader();
                    r.onload = (ev) => {
                        musicVinylImg = ev.target.result;
                        localStorage.setItem(STORAGE.musicVinyl, musicVinylImg);
                        document.getElementById('vinylImg').src = musicVinylImg;
                        document.querySelector('#musicMiniCenter img').src = musicVinylImg;
                    };
                    r.readAsDataURL(f);
                }
            };
            inp.click();
        };
        
        document.getElementById('minimizeMusicBtn').onclick = () => {
            document.getElementById('musicTogetherPanel').classList.remove('open');
            document.getElementById('musicMiniVinyl').classList.add('show');
        };
        
        document.getElementById('musicMiniVinyl').onclick = () => {
            document.getElementById('musicTogetherPanel').classList.add('open');
            document.getElementById('overlay2').classList.add('show');
        };
        
        document.getElementById('closeTogetherBtn').onclick = () => {
            document.getElementById('musicTogetherPanel').classList.remove('open');
            document.getElementById('musicMiniVinyl').classList.remove('show');
            document.getElementById('overlay2').classList.remove('show');
            if(currentAudio) { currentAudio.pause(); isPlayingMusic = false; updateMusicUI(false); }
            isTogetherListening = false;
        };
        
        if(musicVinylImg) {
            document.getElementById('vinylImg').src = musicVinylImg;
            document.querySelector('#musicMiniCenter img').src = musicVinylImg;
        }
    }

    function schedulePartnerRedPacket() {
        setTimeout(() => {
            if(document.hidden) { schedulePartnerRedPacket(); return; }
            let amount = (Math.random() * 20 + 5).toFixed(2);
            let bless = ['520快乐', '想你了', '买杯奶茶', '今天也要开心', '爱你'][Math.floor(Math.random()*5)];
            addMessage('redEnvelope', '', false, { bless, amount: parseFloat(amount), opened: false });
            addMessage('text', `🧧 给你发了一个红包`, false);
            schedulePartnerRedPacket();
        }, Math.floor(Math.random() * (30 * 60 * 1000 - 10 * 60 * 1000) + 10 * 60 * 1000));
    }

    function renderEmojiList() {
        let container = document.getElementById('emojiList');
        if(!container) return;
        container.innerHTML = '';
        emojis.forEach((emoji, idx) => {
            let div = document.createElement('div');
            div.style.position = 'relative';
            let img = document.createElement('img');
            img.src = emoji;
            img.className = 'emoji-thumb';
            let del = document.createElement('span');
            del.innerText = '✕';
            del.style.position = 'absolute'; del.style.top = '0'; del.style.right = '0';
            del.style.background = 'rgba(0,0,0,0.5)'; del.style.color = 'white'; del.style.borderRadius = '50%';
            del.style.cursor = 'pointer'; del.style.padding = '2px 4px'; del.style.fontSize = '10px';
            del.onclick = () => {
                emojis.splice(idx, 1);
                localStorage.setItem(STORAGE.emojis, JSON.stringify(emojis));
                renderEmojiList();
            };
            div.appendChild(img); div.appendChild(del);
            container.appendChild(div);
        });
        if(emojis.length === 0) container.innerHTML = '<div style="color:#aaa;">暂无表情包，点击上方按钮添加</div>';
    }
    function addEmojiFromFile() {
        let input = document.createElement('input');
        input.type = 'file';
        input.accept = 'image/*';
        input.onchange = e => {
            let file = e.target.files[0];
            if(file) {
                compressImage(file, 300, 0.7, (dataUrl) => {
                    emojis.push(dataUrl);
                    localStorage.setItem(STORAGE.emojis, JSON.stringify(emojis));
                    renderEmojiList();
                });
            }
        };
        input.click();
    }
    let emojiPickerVisible = false;
    function showEmojiPicker() {
        if(emojiPickerVisible) return;
        let picker = document.createElement('div');
        picker.className = 'emoji-picker';
        picker.innerHTML = '<div style="display:flex; justify-content:space-between;"><strong>表情包</strong><button id="closeEmojiPicker" style="background:none; border:none; font-size:20px;">✕</button></div><div class="emoji-grid"></div>';
        let grid = picker.querySelector('.emoji-grid');
        if(emojis.length === 0) {
            grid.innerHTML = '<div style="text-align:center; color:#aaa; padding:20px;">暂无表情包，请去设置添加</div>';
            setTimeout(() => {
                if(picker && picker.parentNode) {
                    picker.remove();
                    emojiPickerVisible = false;
                }
            }, 1500);
        } else {
            emojis.forEach((emoji) => {
                let img = document.createElement('img');
                img.src = emoji;
                img.className = 'emoji-item';
                img.onclick = () => {
                    sendImageMessage(emoji);
                    picker.remove();
                    emojiPickerVisible = false;
                };
                grid.appendChild(img);
            });
        }
        document.body.appendChild(picker);
        emojiPickerVisible = true;
        document.getElementById('closeEmojiPicker').onclick = () => { picker.remove(); emojiPickerVisible = false; };
    }

    const stickers = ['🦋', '🌷', '✨', '⭐', '🐣', '🍬', '🎀', '💖', '🍰', '🧸', '🎈', '🌸'];
    function getRandomSticker() { return stickers[Math.floor(Math.random() * stickers.length)]; }
    
    function partnerReplyToComment(momentId, userCommentContent, replyToName) {
        let reply = getSmartReply('moment_comment_' + momentId, userCommentContent);
        let moment = moments.find(m => m.id === momentId);
        if(moment) {
            let content = replyToName ? `回复 ${replyToName}: ${reply}` : reply;
            moment.comments.push({ name: partnerData.name, content: content });
            localStorage.setItem(STORAGE.moments, JSON.stringify(moments));
            renderMoments();
            addMessage('text', `💬 ${partnerData.name} 回复了你的评论`, false);
        }
    }
    function handleUserComment(momentId, commentText, replyToName = null) {
        let moment = moments.find(m => m.id === momentId);
        if(!moment) return;
        if(replyToName) moment.comments.push({ name: myData.name, content: `回复 ${replyToName}: ${commentText}` });
        else moment.comments.push({ name: myData.name, content: commentText });
        localStorage.setItem(STORAGE.moments, JSON.stringify(moments));
        renderMoments();
        setTimeout(() => { partnerReplyToComment(momentId, commentText, replyToName); }, Math.floor(Math.random() * 4000) + 2000);
    }
    
    function renderMoments() {
        let container = document.getElementById('momentPanel');
        if(!container) return;
        if(momentBackground) {
            container.style.backgroundImage = `url(${momentBackground})`;
            container.style.backgroundSize = 'cover';
            container.style.backgroundPosition = 'center';
        } else {
            container.style.backgroundImage = 'none';
            container.style.backgroundColor = 'var(--theme-bg)';
        }
        container.innerHTML = `<div class="publish-area"><textarea id="newMomentInput" class="publish-textarea" rows="2" placeholder="分享此刻的心情... 🌸"></textarea><button id="postMomentBtn" class="post-btn">✨ 发布</button></div>`;
        
        moments.slice().reverse().forEach(m => {
            let div = document.createElement('div'); 
            div.className = 'moment-item';
            let avatarHtml = '';
            let nameHtml = '';
            let isMyMoment = (m.name === myData.name || m.name === '我');
            if(isMyMoment) {
                avatarHtml = (myData.avatar && myData.avatar.startsWith('data:image')) ? `<img src="${myData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : (myData.avatar || '😊');
                nameHtml = myData.name;
            } else {
                avatarHtml = (partnerData.avatar && partnerData.avatar.startsWith('data:image')) ? `<img src="${partnerData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : (partnerData.avatar || '❤️');
                nameHtml = partnerData.name;
            }
            let sticker = getRandomSticker();
            let deleteBtn = isMyMoment ? `<button class="delete-moment" data-id="${m.id}">🗑️</button>` : '';
            let likedByMe = m.likes.includes(myData.name);
            div.innerHTML = `
                <div class="moment-header"><div class="moment-avatar">${avatarHtml}</div><div><div class="moment-name">${escapeHtml(nameHtml)}</div><div class="moment-time">${formatMsgTime(new Date(m.time))}</div></div>${deleteBtn}</div>
                <div class="moment-content" style="position:relative;">${escapeHtml(m.content)}<<span class="moment-sticker">${sticker}</span></div>
                <div class="moment-actions"><span class="like-btn ${likedByMe ? 'liked' : ''}" data-id="${m.id}">${likedByMe ? '💖' : '💗'} ${m.likes.length} 赞</span> <span class="comment-toggle" data-id="${m.id}">💬 ${m.comments.length}</span></div>
                <div id="comments-${m.id}" class="comment-list">${(m.comments||[]).map(c => `<div class="comment-item"><span><strong>${escapeHtml(c.name)}</strong>: ${escapeHtml(c.content)}</span><span class="comment-reply" data-mid="${m.id}" data-cname="${c.name}">回复</span></div>`).join('')}</div>
                <div class="comment-input-area"><input type="text" id="commentInput-${m.id}" class="comment-input" placeholder="写评论..."></div>
            `;
            container.appendChild(div);
        });
        
        document.querySelectorAll('.delete-moment').forEach(btn => { 
            btn.onclick = () => { 
                let id = parseInt(btn.dataset.id); 
                if(confirm('删除这条动态吗？')) { 
                    moments = moments.filter(m => m.id !== id); 
                    localStorage.setItem(STORAGE.moments, JSON.stringify(moments)); 
                    renderMoments(); 
                } 
            }; 
        });
        document.querySelectorAll('.like-btn').forEach(btn => { 
            btn.onclick = () => { 
                let id = parseInt(btn.dataset.id); 
                let m = moments.find(x=>x.id===id); 
                if(m && !m.likes.includes(myData.name)) { 
                    m.likes.push(myData.name); 
                    localStorage.setItem(STORAGE.moments, JSON.stringify(moments)); 
                    renderMoments(); 
                    setTimeout(() => {
                        let reply = getSmartReply('moment_like', '点赞');
                        addMessage('text', reply, false);
                    }, 1500);
                } 
            }; 
        });
        document.querySelectorAll('.comment-input').forEach(inp => { 
            let id = parseInt(inp.id.split('-')[1]); 
            inp.onkeypress = (e) => { 
                if(e.key === 'Enter' && inp.value.trim()) { 
                    let commentText = inp.value.trim(); 
                    inp.value = ''; 
                    handleUserComment(id, commentText, null); 
                } 
            }; 
        });
        document.querySelectorAll('.comment-reply').forEach(btn => { 
            btn.onclick = (e) => { 
                let mid = parseInt(btn.dataset.mid); 
                let replyToName = btn.dataset.cname; 
                let replyText = prompt(`回复 ${replyToName}：`, '🥰'); 
                if(replyText && replyText.trim()) handleUserComment(mid, replyText.trim(), replyToName); 
            }; 
        });
        
        let postBtn = document.getElementById('postMomentBtn');
        if(postBtn) {
            postBtn.onclick = () => { 
                let txt = document.getElementById('newMomentInput').value.trim(); 
                if(txt){ 
                    let newMoment = { id: generateId(), name: myData.name, avatar: myData.avatar, content: txt, time: new Date(), likes: [], comments: [] };
                    moments.unshift(newMoment); 
                    localStorage.setItem(STORAGE.moments, JSON.stringify(moments)); 
                    renderMoments(); 
                    setTimeout(()=>{ 
                        let reply = getSmartReply('moment_new', txt); 
                        let target = moments.find(m => m.id === newMoment.id);
                        if(target) { 
                            target.comments.push({ name: partnerData.name, content: reply }); 
                            localStorage.setItem(STORAGE.moments, JSON.stringify(moments)); 
                            renderMoments(); 
                            addMessage('text', `🌸 ${partnerData.name} 评论了你的动态`, false); 
                        } 
                    }, Math.floor(Math.random()*6000)+3000); 
                } 
            };
        }
    }

    function schedulePartnerMoment() {
        if(partnerMomentTimer) clearTimeout(partnerMomentTimer);
        let delay = Math.floor(Math.random() * (12 * 60 * 1000 - 3 * 60 * 1000) + 3 * 60 * 1000);
        partnerMomentTimer = setTimeout(() => {
            if(document.hidden) { schedulePartnerMoment(); return; }
            let cards = getAllCards();
            let contents = cards.length > 0 ? cards : ["今天天气真好~", "想你了", "在干嘛呢", "看到一只小蝴蝶 🦋", "刚吃了好吃的", "有点想你了", "今天很开心"];
            let content = contents[Math.floor(Math.random() * contents.length)];
            let newMoment = { id: generateId(), name: partnerData.name, avatar: partnerData.avatar, content: content, time: new Date(), likes: [], comments: [] };
            moments.unshift(newMoment);
            localStorage.setItem(STORAGE.moments, JSON.stringify(moments));
            renderMoments();
            addMessage('text', `🌸 ${partnerData.name} 刚发了一条朋友圈`, false);
            schedulePartnerMoment();
        }, delay);
    }

    function schedulePartnerComment() {
        if(partnerCommentTimer) clearTimeout(partnerCommentTimer);
        let delay = Math.floor(Math.random() * (20 * 60 * 1000 - 8 * 60 * 1000) + 8 * 60 * 1000);
        partnerCommentTimer = setTimeout(() => {
            if(document.hidden) { schedulePartnerComment(); return; }
            let userMoments = moments.filter(m => (m.name === myData.name || m.name === '我') && !m.comments.find(c => c.name === partnerData.name));
            if(userMoments.length > 0) {
                let target = userMoments[0];
                let reply = getSmartReply('moment_auto_comment', target.content);
                target.comments.push({ name: partnerData.name, content: reply });
                localStorage.setItem(STORAGE.moments, JSON.stringify(moments));
                renderMoments();
                addMessage('text', `💬 ${partnerData.name} 评论了你的朋友圈`, false);
            }
            schedulePartnerComment();
        }, delay);
    }

    function renderCouple() {
        let container = document.getElementById('couplePanel');
        if(!container) return;
        let start = new Date(couple.anniversary);
        let now = new Date();
        let days = Math.floor((now - start)/(1000*60*60*24));
        let nextYear = new Date(now.getFullYear(), start.getMonth(), start.getDate());
        if(nextYear < now) nextYear.setFullYear(now.getFullYear()+1);
        let countdown = Math.ceil((nextYear - now)/(1000*60*60*24));
        let year = now.getFullYear(), month = now.getMonth();
        let first = new Date(year,month,1).getDay();
        let daysInMonth = new Date(year,month+1,0).getDate();
        let calHtml = '<div class="calendar-grid" style="display:grid; grid-template-columns:repeat(7,1fr); gap:6px; margin-top:16px;">';
        ['日','一','二','三','四','五','六'].forEach(d=>calHtml+=`<div style="font-weight:600; text-align:center;">${d}</div>`);
        for(let i=0;i<first;i++) calHtml+=`<div></div>`;
        for(let d=1;d<=daysInMonth;d++){
            let isMark = (d===start.getDate() && month===start.getMonth());
            calHtml+=`<div style="background:${isMark?'#ffd966':'var(--theme-input-bg)'}; border-radius:20px; padding:6px; text-align:center;">${d}</div>`;
        }
        calHtml+=`</div>`;
        let msgHtml = (couple.messages||[]).slice().reverse().map(m=>`<div class="wall-message"><strong>${escapeHtml(m.name)}</strong> 💬 ${escapeHtml(m.content)}<<div style="font-size:9px; color:#b0a090;">${formatMsgTime(new Date(m.time))}</div></div>`).join('');
        container.innerHTML = `
            <div class="couple-header">
                <div style="display:flex; justify-content:center; gap:30px;"><div style="width:70px;height:70px;border-radius:50%;background:var(--theme-border);display:flex;align-items:center;justify-content:center;font-size:36px;overflow:hidden;">${myData.avatar.startsWith('data:image')?`<img src="${myData.avatar}" style="width:100%;height:100%;border-radius:50%;">`:myData.avatar}</div><div style="width:70px;height:70px;border-radius:50%;background:var(--theme-border);display:flex;align-items:center;justify-content:center;font-size:36px;overflow:hidden;">${partnerData.avatar.startsWith('data:image')?`<img src="${partnerData.avatar}" style="width:100%;height:100%;border-radius:50%;">`:partnerData.avatar}</div></div>
                <div style="font-size:26px; font-weight:700; color:var(--theme-accent); text-align:center; margin:8px 0;">✨ 相爱 ${days} 天 ✨</div>
                <div style="text-align:center;">📅 纪念日: <input type="date" id="anniversaryInput" value="${couple.anniversary}" style="border-radius:20px;"><button id="saveAnniversaryBtn" class="add-card-btn" style="margin-left:8px;">保存</button></div>
                <div style="text-align:center;">⏰ 下一个纪念日还有 ${countdown} 天</div>
                ${calHtml}
            </div>
            <div style="font-weight:600; margin:8px 0 6px;">💌 心情墙</div>
            <div class="message-wall" style="background:var(--theme-input-bg); border-radius:24px; padding:14px; margin-bottom:12px;">${msgHtml || '暂无留言'}</div>
            <textarea id="wallMessageInput" placeholder="写下你的心情..." style="width:100%; border-radius:28px; padding:12px; background:var(--theme-bg); border:0.5px solid var(--theme-border);"></textarea>
            <button id="postWallMsgBtn" class="add-card-btn" style="margin-top:10px;">💕 发布心情</button>
        `;
        document.getElementById('saveAnniversaryBtn').onclick = ()=>{ let newDate = document.getElementById('anniversaryInput').value; if(newDate) { couple.anniversary = newDate; localStorage.setItem(STORAGE.couple,JSON.stringify(couple)); renderCouple(); } };
        document.getElementById('postWallMsgBtn').onclick = ()=>{ 
            let txt = document.getElementById('wallMessageInput').value.trim(); 
            if(txt){ 
                couple.messages = couple.messages || []; 
                couple.messages.push({ name:myData.name, content:txt, time:new Date() }); 
                localStorage.setItem(STORAGE.couple,JSON.stringify(couple)); 
                renderCouple(); 
                setTimeout(()=>{ 
                    let reply = getSmartReply('wall', txt); 
                    couple.messages.push({ name:partnerData.name, content:reply, time:new Date() }); 
                    localStorage.setItem(STORAGE.couple,JSON.stringify(couple)); 
                    renderCouple(); 
                    addMessage('text', '💖 刚看到你的心情', false); 
                }, 3000); 
            } 
        };
    }

    function checkPendingReplies() {
        let now = Date.now();
        let toReply = pendingReplies.filter(p => p.replyAt <= now);
        if(toReply.length > 0) {
            for(let p of toReply) {
                let originalLetter = letters.find(l => l.id === p.id);
                if(originalLetter && originalLetter.from === 'me') {
                    let replyContent = getSmartReply('letter_reply', originalLetter.content);
                    letters.push({ id: generateId(), title: '💖 回信', content: replyContent, from: 'partner', time: new Date() });
                    pendingReplies = pendingReplies.filter(x => x.id !== p.id);
                }
            }
            localStorage.setItem(STORAGE.letters, JSON.stringify(letters));
            localStorage.setItem(STORAGE.pendingReplies, JSON.stringify(pendingReplies));
            renderLetters();
            addMessage('text', '📨 你收到了一封回信，去书信看看吧', false);
        }
    }
    function scheduleReplyCheck() { if(replyCheckInterval) clearInterval(replyCheckInterval); replyCheckInterval = setInterval(() => checkPendingReplies(), 60000); }
    function renderLetters() {
        let container = document.getElementById('letterPanel');
        if(!container) return;
        let minHours = letterDelay.minHours, maxHours = letterDelay.maxHours;
        let listHtml = letters.slice().reverse().map(l => {
            let deleteBtn = `<button class="delete-letter" data-id="${l.id}">🗑️</button>`;
            return `<div class="letter-item"><div class="letter-title">📮 ${escapeHtml(l.title)} <span style="font-size:10px;">— ${l.from==='me'?myData.name:partnerData.name}</span>${deleteBtn}</div><div class="letter-content">${escapeHtml(l.content)}</div><div class="letter-info">✉️ ${formatMsgTime(new Date(l.time))}</div></div>`;
        }).join('');
        container.innerHTML = `
            <div class="delay-settings"><div style="font-weight:600; margin-bottom:6px;">⏱️ 对方回复延迟（小时）</div><div class="delay-slider"><span>最短</span><input type="range" id="minHourSlider" min="0.5" max="72" step="0.5" value="${minHours}"><span id="minHourVal">${minHours}</span><span>最长</span><input type="range" id="maxHourSlider" min="1" max="168" step="0.5" value="${maxHours}"><span id="maxHourVal">${maxHours}</span><button id="saveDelayBtn" class="add-card-btn" style="background:#c29ebf;">保存</button></div></div>
            <div class="letter-header"><div class="letter-avatar" id="letterMyAvatar">${myData.avatar.startsWith('data:image')?`<img src="${myData.avatar}" style="width:100%;height:100%;border-radius:50%;">`:myData.avatar}</div><div><div style="font-weight:600;">${escapeHtml(myData.name)}</div><div style="font-size:10px;">寄信人</div></div><div style="margin-left:auto;">🌙 →</div><div class="letter-avatar" id="letterPartnerAvatar">${partnerData.avatar.startsWith('data:image')?`<img src="${partnerData.avatar}" style="width:100%;height:100%;border-radius:50%;">`:partnerData.avatar}</div><div><div style="font-weight:600;">${escapeHtml(partnerData.name)}</div><div style="font-size:10px;">收信人</div></div></div>
            <div class="write-letter-area"><input id="letterTitle" class="write-letter-input" placeholder="📌 标题"><textarea id="letterContent" class="write-letter-textarea" rows="3" placeholder="写下你想说的话..."></textarea><button id="sendLetterBtn" class="send-btn" style="background:var(--theme-accent); border-radius:30px;">💌 寄出信件</button></div>
            <div style="font-weight:600; margin:8px 0 6px;">📬 书信集</div>${listHtml || '<div style="background:var(--theme-input-bg); border-radius:32px; padding:20px; text-align:center; color:#b0a090;">暂无信件，写一封吧 🌸</div>'}
        `;
        document.querySelectorAll('.delete-letter').forEach(btn => {
            btn.onclick = () => {
                let id = parseInt(btn.dataset.id);
                if(confirm('删除这封信？')) {
                    letters = letters.filter(l => l.id !== id);
                    pendingReplies = pendingReplies.filter(p => p.id !== id);
                    localStorage.setItem(STORAGE.letters, JSON.stringify(letters));
                    localStorage.setItem(STORAGE.pendingReplies, JSON.stringify(pendingReplies));
                    renderLetters();
                }
            };
        });
        document.getElementById('saveDelayBtn').onclick = () => { 
            let min = parseFloat(document.getElementById('minHourSlider').value); 
            let max = parseFloat(document.getElementById('maxHourSlider').value); 
            if(min >= max) { alert('最短必须小于最长'); return; } 
            letterDelay.minHours = min; 
            letterDelay.maxHours = max; 
            localStorage.setItem(STORAGE.letterDelay, JSON.stringify(letterDelay)); 
            alert(`回复延迟已保存：${min}~${max} 小时`); 
        };
        document.getElementById('minHourSlider').oninput = (e) => { document.getElementById('minHourVal').innerText = e.target.value; };
        document.getElementById('maxHourSlider').oninput = (e) => { document.getElementById('maxHourVal').innerText = e.target.value; };
        document.getElementById('sendLetterBtn').onclick = () => {
            let title = document.getElementById('letterTitle').value.trim();
            let content = document.getElementById('letterContent').value.trim();
            if(!title || !content) { alert('请填写标题和内容'); return; }
            let newId = generateId();
            letters.push({ id: newId, title, content, from: 'me', time: new Date() });
            let minMs = letterDelay.minHours * 60 * 60 * 1000;
            let maxMs = letterDelay.maxHours * 60 * 60 * 1000;
            let delayMs = Math.floor(Math.random() * (maxMs - minMs + 1) + minMs);
            let replyAt = Date.now() + delayMs;
            pendingReplies.push({ id: newId, replyAt });
            localStorage.setItem(STORAGE.letters, JSON.stringify(letters));
            localStorage.setItem(STORAGE.pendingReplies, JSON.stringify(pendingReplies));
            renderLetters();
            document.getElementById('letterTitle').value = '';
            document.getElementById('letterContent').value = '';
            addMessage('text', `📮 信已寄出，对方将在 ${Math.round(delayMs/3600000)} 小时左右回复`, false);
        };
    }

    function renderCardPanel() {
        const container = document.getElementById('cardPanel');
        if(!container) return;
        let html = '';
        for(let idx = 0; idx < cardGroups.length; idx++) {
            const group = cardGroups[idx];
            html += `<div class="card-group" data-group-index="${idx}">
                        <div class="card-group-title">
                            <span>🍬 ${escapeHtml(group.name)}</span>
                            <button class="delete-group-btn" data-group="${idx}">删除分组</button>
                        </div>
                        <div class="batch-import-area">
                            <textarea id="batchText_${idx}" class="batch-textarea" rows="2" placeholder="每行一句，批量导入（自动去重）"></textarea>
                            <button class="batch-import-btn" data-group="${idx}">📥 批量导入</button>
                        </div>
                        <div class="card-items">`;
            for(let ci = 0; ci < group.cards.length; ci++) {
                html += `<div class="card-chip" data-group="${idx}" data-card="${ci}">
                            <span>${escapeHtml(group.cards[ci])}</span>
                            <span class="delete-card" data-group="${idx}" data-card="${ci}">✕</span>
                        </div>`;
            }
            html += `</div></div>`;
        }
        container.innerHTML = html;

        document.querySelectorAll('.card-chip').forEach(el => {
            el.onclick = (e) => {
                if(e.target.classList.contains('delete-card')) return;
                const g = parseInt(el.dataset.group);
                const c = parseInt(el.dataset.card);
                sendUserMessage(cardGroups[g].cards[c]);
            };
        });
        document.querySelectorAll('.delete-card').forEach(el => {
            el.onclick = (e) => {
                e.stopPropagation();
                const g = parseInt(el.dataset.group);
                const c = parseInt(el.dataset.card);
                cardGroups[g].cards.splice(c, 1);
                if(cardGroups[g].cards.length === 0) cardGroups.splice(g, 1);
                localStorage.setItem(STORAGE.cardGroups, JSON.stringify(cardGroups));
                renderCardPanel();
            };
        });
        document.querySelectorAll('.delete-group-btn').forEach(btn => {
            btn.onclick = () => {
                const g = parseInt(btn.dataset.group);
                cardGroups.splice(g, 1);
                localStorage.setItem(STORAGE.cardGroups, JSON.stringify(cardGroups));
                renderCardPanel();
            };
        });
        document.querySelectorAll('.batch-import-btn').forEach(btn => {
            btn.onclick = () => {
                const g = parseInt(btn.dataset.group);
                const raw = document.getElementById(`batchText_${g}`).value;
                if(!raw.trim()) { alert("请输入内容，每行一句"); return; }
                const lines = raw.split(/\r?\n/);
                let added = 0, skipped = 0;
                const existingSet = new Set(cardGroups[g].cards);
                for(let line of lines) {
                    line = line.trim();
                    if(line === "") continue;
                    if(existingSet.has(line)) {
                        skipped++;
                    } else {
                        cardGroups[g].cards.push(line.slice(0, 25));
                        existingSet.add(line);
                        added++;
                    }
                }
                if(added > 0) {
                    localStorage.setItem(STORAGE.cardGroups, JSON.stringify(cardGroups));
                    renderCardPanel();
                    alert(`✅ 成功导入 ${added} 条新字卡，跳过重复 ${skipped} 条`);
                } else if(skipped > 0) {
                    alert(`⚠️ 全部重复，未添加任何新字卡（跳过 ${skipped} 条）`);
                } else {
                    alert("没有有效的非空行");
                }
            };
        });
    }

    function applySettings() {
        document.getElementById('partnerNameDisplay').innerText = partnerData.name;
        let headerAv = document.getElementById('myAvatarHeader');
        if(myData.avatar.startsWith('data:image')) headerAv.innerHTML = `<img src="${myData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`;
        else headerAv.innerText = myData.avatar;
        document.querySelector('.chat-main').style.backgroundImage = chatBackground ? `url(${chatBackground})` : 'none';
        if(momentBackground) {
            document.getElementById('momentPanel').style.backgroundImage = `url(${momentBackground})`;
            document.getElementById('momentPanel').style.backgroundSize = 'cover';
            document.getElementById('momentPanel').style.backgroundPosition = 'center';
        } else {
            document.getElementById('momentPanel').style.backgroundImage = 'none';
            document.getElementById('momentPanel').style.backgroundColor = 'var(--theme-bg)';
        }
        renderEmojiList();
        document.getElementById('previewPartnerAvatar').innerHTML = partnerData.avatar.startsWith('data:image') ? `<img src="${partnerData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : partnerData.avatar;
        document.getElementById('previewMyAvatar').innerHTML = myData.avatar.startsWith('data:image') ? `<img src="${myData.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : myData.avatar;
    }
    function saveAll() {
        localStorage.setItem(STORAGE.partnerName, partnerData.name);
        localStorage.setItem(STORAGE.partnerAvatar, partnerData.avatar);
        localStorage.setItem(STORAGE.myAvatar, myData.avatar);
        localStorage.setItem(STORAGE.myName, myData.name);
        localStorage.setItem(STORAGE.chatBg, chatBackground);
        localStorage.setItem(STORAGE.momentBg, momentBackground);
        localStorage.setItem(STORAGE.callBg, callBackground);
        localStorage.setItem(STORAGE.patSuffix, patSuffix);
        localStorage.setItem(STORAGE.cardGroups, JSON.stringify(cardGroups));
        localStorage.setItem(STORAGE.moments, JSON.stringify(moments));
        localStorage.setItem(STORAGE.couple, JSON.stringify(couple));
        localStorage.setItem(STORAGE.letters, JSON.stringify(letters));
        localStorage.setItem(STORAGE.pendingReplies, JSON.stringify(pendingReplies));
        localStorage.setItem(STORAGE.letterDelay, JSON.stringify(letterDelay));
        localStorage.setItem(STORAGE.emojis, JSON.stringify(emojis));
        localStorage.setItem(STORAGE.chatRhythm, JSON.stringify(chatRhythm));
        localStorage.setItem(STORAGE.maxConsecutive, maxConsecutive);
        applySettings();
    }
    function changeAvatar(type) {
        let inp = document.createElement('input'); inp.type='file'; inp.accept='image/*';
        inp.onchange = e => { 
            let f=e.target.files[0]; 
            if(f){ 
                compressImage(f, 400, 0.8, (img) => {
                    if(type==='partner'){ 
                        partnerData.avatar=img; 
                        document.getElementById('previewPartnerAvatar').innerHTML=`<img src="${img}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`; 
                    } else if(type==='my'){ 
                        myData.avatar=img; 
                        document.getElementById('previewMyAvatar').innerHTML=`<img src="${img}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`; 
                    } 
                    saveAll(); 
                    renderLetters(); 
                    renderCouple(); 
                    renderMoments(); 
                    renderMessages(); 
                });
            } 
        };
        inp.click();
    }
    function changeBackground() {
        let inp = document.createElement('input'); inp.type='file'; inp.accept='image/*';
        inp.onchange = e => { 
            let f=e.target.files[0]; 
            if(f){ 
                compressImage(f, 800, 0.6, (img) => {
                    chatBackground=img; 
                    saveAll(); 
                });
            } 
        };
        inp.click();
    }
    function changeMomentBg() {
        let inp = document.createElement('input'); inp.type='file'; inp.accept='image/*';
        inp.onchange = e => { 
            let f=e.target.files[0]; 
            if(f){ 
                compressImage(f, 800, 0.6, (img) => {
                    momentBackground=img; 
                    saveAll(); 
                    renderMoments(); 
                });
            } 
        };
        inp.click();
    }
    function changeCallBg() {
        let inp = document.createElement('input'); inp.type='file'; inp.accept='image/*';
        inp.onchange = e => { 
            let f=e.target.files[0]; 
            if(f){ 
                compressImage(f, 800, 0.6, (img) => {
                    callBackground=img; 
                    localStorage.setItem(STORAGE.callBg, callBackground);
                    alert('通话背景已设置');
                });
            } 
        };
        inp.click();
    }
    function loadSettings() {
        document.getElementById('minDelaySec').value = chatRhythm.minDelay;
        document.getElementById('maxDelaySec').value = chatRhythm.maxDelay;
        document.getElementById('maxConsecutive').value = maxConsecutive;
        document.getElementById('letterMinHour').value = letterDelay.minHours;
        document.getElementById('letterMaxHour').value = letterDelay.maxHours;
        document.getElementById('patSuffixInput').value = patSuffix;
        document.getElementById('myBalanceInput').value = myBalance;
        document.getElementById('partnerBalanceInput').value = partnerBalance;
    }
    function saveChatSettings() {
        chatRhythm.minDelay = parseFloat(document.getElementById('minDelaySec').value);
        chatRhythm.maxDelay = parseFloat(document.getElementById('maxDelaySec').value);
        maxConsecutive = parseInt(document.getElementById('maxConsecutive').value);
        localStorage.setItem(STORAGE.chatRhythm, JSON.stringify(chatRhythm));
        localStorage.setItem(STORAGE.maxConsecutive, maxConsecutive);
        letterDelay.minHours = parseFloat(document.getElementById('letterMinHour').value);
        letterDelay.maxHours = parseFloat(document.getElementById('letterMaxHour').value);
        localStorage.setItem(STORAGE.letterDelay, JSON.stringify(letterDelay));
        patSuffix = document.getElementById('patSuffixInput').value || '肩膀';
        localStorage.setItem(STORAGE.patSuffix, patSuffix);
        let newMyBal = parseFloat(document.getElementById('myBalanceInput').value);
        let newPtBal = parseFloat(document.getElementById('partnerBalanceInput').value);
        if(!isNaN(newMyBal)) { myBalance = newMyBal; localStorage.setItem(STORAGE.balance + '_my', myBalance); }
        if(!isNaN(newPtBal)) { partnerBalance = newPtBal; localStorage.setItem(STORAGE.balance + '_partner', partnerBalance); }
        alert('设置已保存');
    }

    function switchTab(tab) {
        let panels = ['cardPanel','momentPanel','couplePanel','letterPanel','settingsTabPanel'];
        panels.forEach(p=>document.getElementById(p).style.display='none');
        document.querySelector('.chat-main').style.display='none';
        document.getElementById('inputBar').style.display='none';
        document.getElementById('quoteIndicator').style.display = 'none';
        if(tab === 'chat') { 
            document.querySelector('.chat-main').style.display='flex'; 
            document.getElementById('inputBar').style.display='flex'; 
        }
        else if(tab === 'card') { document.getElementById('cardPanel').style.display='block'; renderCardPanel(); }
        else if(tab === 'settings') { document.getElementById('settingsTabPanel').style.display='block'; }
        else if(tab === 'moment') { document.getElementById('momentPanel').style.display='block'; renderMoments(); }
        else if(tab === 'couple') { document.getElementById('couplePanel').style.display='block'; renderCouple(); }
        else if(tab === 'letter') { document.getElementById('letterPanel').style.display='block'; renderLetters(); }
        document.querySelectorAll('.tab-item').forEach(t=>t.classList.remove('active'));
        let targetTab = document.querySelector(`.tab-item[data-tab="${tab}"]`);
        if(targetTab) targetTab.classList.add('active');
    }

    function renderDressPanel() {
        let themeGrid = document.getElementById('themeGrid');
        themeGrid.innerHTML = '';
        Object.keys(THEMES).forEach(key => {
            let t = THEMES[key];
            let div = document.createElement('div');
            div.className = 'theme-block' + (currentTheme === key ? ' active' : '');
            div.style.background = `linear-gradient(135deg, ${t.accent}, ${t.bubbleUser})`;
            div.innerText = t.name;
            div.onclick = () => {
                currentTheme = key;
                localStorage.setItem(STORAGE.theme, key);
                applyTheme(key);
                renderDressPanel();
            };
            themeGrid.appendChild(div);
        });
        let styleGrid = document.getElementById('styleGrid');
        styleGrid.innerHTML = '';
        BUBBLE_STYLES.forEach(s => {
            let div = document.createElement('div');
            div.className = 'style-block' + (currentBubbleStyle === s.key ? ' active' : '');
            div.innerText = s.name;
            div.onclick = () => {
                currentBubbleStyle = s.key;
                localStorage.setItem(STORAGE.bubbleStyle, s.key);
                applyBubbleStyle(s.key);
                renderDressPanel();
            };
            styleGrid.appendChild(div);
        });
        let fontGrid = document.getElementById('fontGrid');
        fontGrid.innerHTML = '';
        FONT_STYLES.forEach(f => {
            let div = document.createElement('div');
            div.className = 'font-block' + (currentFontStyle === f.key ? ' active' : '');
            div.innerText = f.name;
            div.style.fontFamily = f.key === 'default' ? 'sans-serif' : (f.key === 'handwriting' ? 'cursive' : (f.key === 'cute' ? 'fantasy' : 'KaiTi'));
            div.onclick = () => {
                currentFontStyle = f.key;
                localStorage.setItem(STORAGE.fontStyle, f.key);
                applyFontStyle(f.key);
                renderDressPanel();
            };
            fontGrid.appendChild(div);
        });
    }

    // ===== 占卜系统数据 =====
    const TAROT_DECK = [
        { name: '恋人', love: '桃花运旺盛，感情甜蜜，适合表白', meaning: '爱情、结合、选择', icon: '💕' },
        { name: '太阳', love: '充满阳光，关系明朗，幸福满满', meaning: '成功、喜悦、活力', icon: '☀️' },
        { name: '月亮', love: '有些暧昧不明，需要更多沟通', meaning: '幻觉、不安、潜意识', icon: '🌙' },
        { name: '星星', love: '希望之光，暗恋可能有回应', meaning: '希望、灵感、宁静', icon: '⭐' },
        { name: '命运之轮', love: '缘分天注定，转角遇到爱', meaning: '转变、周期、命运', icon: '☸️' },
        { name: '力量', love: '用温柔征服TA，以柔克刚', meaning: '勇气、耐心、内在力量', icon: '💪' },
        { name: '隐士', love: '需要独处思考，暂时保持距离', meaning: '内省、孤独、智慧', icon: '🧙' },
        { name: '皇后', love: '丰盛的爱，关系稳定且滋养', meaning: '丰饶、母性、自然', icon: '👑' },
        { name: '皇帝', love: '关系进入稳定期，责任感强', meaning: '权威、结构、控制', icon: '🤴' },
        { name: '节制', love: '平衡发展，不要操之过急', meaning: '平衡、调和、耐心', icon: '🏺' },
        { name: '恶魔', love: '警惕占有欲，别让爱变成束缚', meaning: '欲望、束缚、物质', icon: '👿' },
        { name: '塔', love: '可能有突发状况，需要坦诚面对', meaning: '突变、觉醒、破坏', icon: '🗼' },
        { name: '世界', love: '圆满的结局，修成正果', meaning: '完成、圆满、成就', icon: '🌍' },
        { name: '审判', love: '旧情复燃或关系重生', meaning: '重生、觉醒、召唤', icon: '📯' },
        { name: '死神', love: '一段关系结束，新的开始', meaning: '结束、转化、新生', icon: '💀' },
        { name: '倒吊人', love: '牺牲与等待，换个角度看问题', meaning: '牺牲、等待、视角转换', icon: '🙃' },
        { name: '战车', love: '主动出击，勇敢追求所爱', meaning: '意志、胜利、控制', icon: '🏎️' },
        { name: '正义', love: '公平对待，理性处理感情问题', meaning: '公正、真理、因果', icon: '⚖️' },
        { name: '愚者', love: '新的冒险，勇敢去爱吧', meaning: '开始、冒险、纯真', icon: '🃏' },
        { name: '魔术师', love: '展现魅力，你有能力把握爱情', meaning: '创造、技能、自信', icon: '🎩' },
        { name: '女祭司', love: '直觉敏锐，相信你的第六感', meaning: '直觉、神秘、智慧', icon: '🔮' },
        { name: '高塔', love: '打破幻想，看清真实', meaning: '突变、真相、解放', icon: '⚡' }
    ];

    const DIVINATION_STICKS = [
        { level: '上上签', poem: '关关雎鸠在河之洲，窈窕淑女君子好逑。', explain: '桃花运极旺，心仪之人即将出现，或有喜事临门。' },
        { level: '上吉签', poem: '两情若是久长时，又岂在朝朝暮暮。', explain: '感情稳固，虽有距离但心意相通，坚持就是胜利。' },
        { level: '中吉签', poem: '山有木兮木有枝，心悦君兮君不知。', explain: '暗恋有望修成正果，勇敢表达心意会有好结果。' },
        { level: '中平签', poem: '曾经沧海难为水，除却巫山不是云。', explain: '感情进入平淡期，需要制造新鲜感来升温。' },
        { level: '小吉签', poem: '身无彩凤双飞翼，心有灵犀一点通。', explain: '默契度提升，适合深度交流，增进了解。' },
        { level: '末吉签', poem: '落花有意随流水，流水无心恋落花。', explain: '暂时有些单相思，建议调整心态，顺其自然。' },
        { level: '下签', poem: '多情自古伤离别，更那堪冷落清秋节。', explain: '近期感情有些波折，需要多沟通，避免误会。' },
        { level: '上上签', poem: '愿得一心人，白头不相离。', explain: '真爱降临，有望步入稳定关系或婚姻殿堂。' }
    ];

    const WEEKLY_FORTUNES = ['桃花运爆棚，适合主动出击', '财运亨通，适合约会消费', '事业学业顺利，感情稳定', '注意身体健康，多关心对方', '适合表白的好日子', '容易有口角，说话注意分寸', '贵人运旺，朋友介绍对象', '旧情可能复燃，慎重选择'];

    function initFortune() {
        let smokeContainer = document.getElementById('crystalSmoke');
        for(let i=0; i<<6; i++) {
            let p = document.createElement('div');
            p.className = 'smoke-particle';
            p.style.width = (25 + Math.random()*35) + 'px';
            p.style.height = p.style.width;
            p.style.left = (Math.random()*140) + 'px';
            p.style.top = (Math.random()*80 + 40) + 'px';
            p.style.animation = `smokeRise ${3 + Math.random()*2}s ease-in-out infinite`;
            p.style.animationDelay = (i*0.4) + 's';
            smokeContainer.appendChild(p);
        }

        document.getElementById('crystalBall').onclick = () => {
            let card = TAROT_DECK[Math.floor(Math.random() * TAROT_DECK.length)];
            let cardDiv = document.createElement('div');
            cardDiv.style.cssText = 'position:fixed;top:50%;left:50%;transform:translate(-50%,-50%) scale(0);background:linear-gradient(135deg,#f3e8ff,#e9d5ff);border-radius:16px;padding:24px;width:220px;text-align:center;z-index:350;box-shadow:0 20px 60px rgba(168,85,247,0.4);border:2px solid #c084fc;transition:transform 0.5s cubic-bezier(0.34,1.56,0.64,1);';
            cardDiv.innerHTML = `
                <div style="font-size:48px;margin-bottom:8px;">${card.icon}</div>
                <div style="font-size:20px;font-weight:700;color:#581c87;margin-bottom:6px;">${card.name}</div>
                <div style="font-size:13px;color:#7c3aed;margin-bottom:8px;">${card.meaning}</div>
                <div style="font-size:14px;color:#4c1d95;line-height:1.5;">${card.love}</div>
                <button style="margin-top:12px;background:#a855f7;color:white;border:none;border-radius:20px;padding:8px 20px;font-weight:600;cursor:pointer;">收下这张牌</button>
            `;
            document.body.appendChild(cardDiv);
            setTimeout(() => cardDiv.style.transform = 'translate(-50%,-50%) scale(1)', 50);
            
            cardDiv.querySelector('button').onclick = () => {
                cardDiv.style.transform = 'translate(-50%,-50%) scale(0)';
                setTimeout(() => cardDiv.remove(), 300);
            };
        };

        // 本周运势：改为抽一张塔罗卡牌
        document.getElementById('modeWeekly').onclick = () => {
            document.getElementById('weeklyFortune').style.display = 'block';
            document.getElementById('tarotArea').style.display = 'none';
            document.getElementById('stickArea').style.display = 'none';
            
            let card = TAROT_DECK[Math.floor(Math.random() * TAROT_DECK.length)];
            let html = `
                <div style="display:flex; justify-content:center; margin:16px 0;">
                    <div class="tarot-card-item revealed" style="width:120px; height:180px; font-size:14px; cursor:default;">
                        <div class="tarot-card-pattern" style="font-size:80px; opacity:0.25;">${card.icon}</div>
                        <div class="tarot-card-name" style="font-size:16px;">${card.name}</div>
                        <div class="tarot-card-mean" style="font-size:12px;">${card.meaning}</div>
                    </div>
                </div>
                <div style="background:var(--theme-header); border-radius:16px; padding:14px; border:1px solid var(--theme-border); text-align:center;">
                    <div style="font-weight:600; color:var(--theme-accent); margin-bottom:8px;">✨ 本周指引</div>
                    <div style="color:var(--theme-status); font-size:14px; line-height:1.5;">${card.love}</div>
                </div>
            `;
            document.getElementById('weeklyContent').innerHTML = html;
        };

        // 塔罗：改为3张，必须输入问题
        document.getElementById('modeTarot').onclick = () => {
            document.getElementById('weeklyFortune').style.display = 'none';
            document.getElementById('tarotArea').style.display = 'block';
            document.getElementById('stickArea').style.display = 'none';
            document.getElementById('tarotTable').innerHTML = '';
            document.getElementById('tarotResult').style.display = 'none';
        };

        document.getElementById('drawTarotBtn').onclick = () => {
            let question = document.getElementById('tarotQuestion').value.trim();
            if(!question) { alert('请先写下你想问的问题哦~'); return; }
            
            let table = document.getElementById('tarotTable');
            table.innerHTML = '';
            let resultDiv = document.getElementById('tarotResult');
            
            let deck = [...TAROT_DECK];
            let selected = [];
            for(let i=0; i<<3; i++) {
                let idx = Math.floor(Math.random() * deck.length);
                selected.push(deck[idx]);
                deck.splice(idx, 1);
            }
            
            let positions = ['过去', '现在', '未来'];
            selected.forEach((card, i) => {
                let div = document.createElement('div');
                div.className = 'tarot-card-item';
                div.innerHTML = `
                    <div class="tarot-card-pattern">${card.icon}</div>
                    <div class="tarot-card-name">${card.name}</div>
                    <div class="tarot-card-mean">${positions[i]}</div>
                `;
                div.style.opacity = '0';
                div.style.transform = 'translateY(20px)';
                table.appendChild(div);
                setTimeout(() => {
                    div.style.transition = 'all 0.5s';
                    div.style.opacity = '1';
                    div.style.transform = 'translateY(0)';
                }, 200 + i*200);
            });
            
            let summary = `「过去」${selected[0].name}：${selected[0].love}<br><br>「现在」${selected[1].name}：${selected[1].love}<br><br>「未来」${selected[2].name}：${selected[2].love}`;
            resultDiv.innerHTML = `<div style="background:var(--theme-header); border-radius:16px; padding:14px; border:1px solid var(--theme-border);"><div style="font-weight:600; color:var(--theme-accent); margin-bottom:6px;">✨ 三牌阵解读</div><div style="color:var(--theme-status); font-size:13px; line-height:1.5;">${summary}</div></div>`;
            resultDiv.style.display = 'block';
        };

        // 灵签：必须输入问题
        document.getElementById('modeStick').onclick = () => {
            document.getElementById('weeklyFortune').style.display = 'none';
            document.getElementById('tarotArea').style.display = 'none';
            document.getElementById('stickArea').style.display = 'block';
            document.getElementById('stickResult').style.display = 'none';
        };

        document.getElementById('drawStickBtn').onclick = () => {
            let question = document.getElementById('stickQuestion').value.trim();
            if(!question) { alert('请先在上方输入你想问的问题~'); return; }
            
            let stick = DIVINATION_STICKS[Math.floor(Math.random() * DIVINATION_STICKS.length)];
            let resultDiv = document.getElementById('stickResult');
            resultDiv.innerHTML = `
                <div class="stick-level">${stick.level}</div>
                <div class="stick-poem">"${stick.poem}"</div>
                <div class="stick-explain">💡 ${stick.explain}</div>
            `;
            resultDiv.style.display = 'block';
        };
    }

    function initFocus() {
        // 专注模式内部消息
        function addFocusInternalMessage(text) {
            let container = document.getElementById('focusInternalMsgs');
            if(!container) return;
            let div = document.createElement('div');
            div.style.cssText = 'font-size:12px; color:var(--theme-accent); margin:4px 0; text-align:center; opacity:0.85;';
            div.innerText = text;
            container.appendChild(div);
            container.scrollTop = container.scrollHeight;
            if(container.children.length > 8) container.removeChild(container.firstChild);
        }

        // 实时刷新备注名和头像
        document.getElementById('focusMyName').innerText = myData.name;
        document.getElementById('focusPartnerName').innerText = partnerData.name;
        document.getElementById('focusChatLabelName').innerText = partnerData.name;
        document.getElementById('focusMyAvatar').innerHTML = myData.avatar.startsWith('data:image') ? `<img src="${myData.avatar}">` : myData.avatar;
        document.getElementById('focusPartnerAvatar').innerHTML = partnerData.avatar.startsWith('data:image') ? `<img src="${partnerData.avatar}">` : partnerData.avatar;

        let timerEl = document.getElementById('focusTimerBig');
        let statusEl = document.getElementById('focusStatus');
        let startBtn = document.getElementById('focusStartBtn');
        let pauseBtn = document.getElementById('focusPauseBtn');
        let stopBtn = document.getElementById('focusStopBtn');
        let setupArea = document.getElementById('focusSetupArea');
        let activeArea = document.getElementById('focusActiveArea');
        let chatBox = document.getElementById('focusChatBox');
        let taskDisplay = document.getElementById('focusTaskDisplay');

        function updateTimerDisplay() {
            let m = Math.floor(focusSeconds / 60);
            let s = focusSeconds % 60;
            let text = `${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
            timerEl.innerText = text;
        }

        document.getElementById('focusDayBtn').onclick = function() {
            focusModeNight = false;
            document.getElementById('focusPanel').classList.remove('night-mode');
            this.classList.add('active');
            document.getElementById('focusNightBtn').classList.remove('active');
        };
        document.getElementById('focusNightBtn').onclick = function() {
            focusModeNight = true;
            document.getElementById('focusPanel').classList.add('night-mode');
            this.classList.add('active');
            document.getElementById('focusDayBtn').classList.remove('active');
        };

        startBtn.onclick = () => {
            if(isFocusing && focusPaused) {
                focusPaused = false;
                startBtn.style.display = 'none';
                pauseBtn.style.display = 'inline-block';
                statusEl.innerText = '专注中... 加油！';
                return;
            }
            let task = document.getElementById('focusTask').value.trim();
            if(!task) { 
                alert('请输入专注事项才能开始哦~'); 
                return; 
            }
            // 默认25分钟
            focusSeconds = 25 * 60;
            focusTotal = focusSeconds;
            
            isFocusing = true;
            focusPaused = false;
            setupArea.style.display = 'none';
            activeArea.style.display = 'block';
            chatBox.style.display = 'block';
            pauseBtn.style.display = 'inline-block';
            stopBtn.style.display = 'inline-block';
            statusEl.innerText = `正在专注：${task}`;
            taskDisplay.innerText = task;
            
            // 清除可能存在的旧继续按钮
            let oldContinue = document.getElementById('focusContinueBtn');
            if(oldContinue) oldContinue.remove();
            
            if(focusTimerInterval) clearInterval(focusTimerInterval);
            focusTimerInterval = setInterval(() => {
                if(!focusPaused) {
                    focusSeconds--;
                    updateTimerDisplay();
                    if(focusSeconds <= 0) {
                        clearInterval(focusTimerInterval);
                        isFocusing = false;
                        setupArea.style.display = 'block';
                        activeArea.style.display = 'none';
                        chatBox.style.display = 'none';
                        pauseBtn.style.display = 'none';
                        stopBtn.style.display = 'none';
                        statusEl.innerText = '准备开始专注';
                        document.getElementById('focusTask').value = '';
                    }
                }
            }, 1000);
            updateTimerDisplay();
        };
        
        pauseBtn.onclick = () => {
            focusPaused = true;
            pauseBtn.style.display = 'none';
            let continueBtn = document.createElement('button');
            continueBtn.className = 'focus-btn focus-start';
            continueBtn.id = 'focusContinueBtn';
            continueBtn.innerText = '继续';
            continueBtn.style.display = 'inline-block';
            continueBtn.onclick = () => {
                focusPaused = false;
                continueBtn.remove();
                pauseBtn.style.display = 'inline-block';
                statusEl.innerText = `正在专注：${document.getElementById('focusTask').value.trim()}`;
            };
            pauseBtn.parentNode.appendChild(continueBtn);
            statusEl.innerText = '已暂停';
        };
        
        stopBtn.onclick = () => {
            clearInterval(focusTimerInterval);
            isFocusing = false;
            focusPaused = false;
            focusSeconds = 25 * 60;
            focusTotal = 25 * 60;
            updateTimerDisplay();
            setupArea.style.display = 'block';
            activeArea.style.display = 'none';
            chatBox.style.display = 'none';
            pauseBtn.style.display = 'none';
            stopBtn.style.display = 'none';
            let continueBtn = document.getElementById('focusContinueBtn');
            if(continueBtn) continueBtn.remove();
            statusEl.innerText = '准备开始专注';
            document.getElementById('focusTask').value = '';
        };

        // 戳一戳：只在专注内部显示
        document.getElementById('focusPatBtn').onclick = () => {
            if(!isFocusing) { alert('先开始专注才能戳戳哦~'); return; }
            addFocusInternalMessage(`👋 你戳了戳 ${partnerData.name}的${patSuffix}`);
            setTimeout(() => {
                addFocusInternalMessage(`👋 ${partnerData.name}：专注也要想我哦~`);
            }, 800);
        };

        // 听歌
        document.getElementById('focusMusicBtn').onclick = () => {
            document.getElementById('musicPanel').classList.add('open');
            document.getElementById('overlay2').classList.add('show');
        };

        // 专注模式内发消息：只在内部显示
        document.getElementById('focusSendMsgBtn').onclick = () => {
            let input = document.getElementById('focusMsgInput');
            if(input.value.trim()) {
                addFocusInternalMessage(`💬 你：${input.value.trim()}`);
                // 模拟对方在专注内部回复
                setTimeout(() => {
                    let reply = getSmartReply('focus_internal', input.value.trim());
                    addFocusInternalMessage(`💬 ${partnerData.name}：${reply}`);
                }, 1000 + Math.random() * 2000);
                input.value = '';
            }
        };
        document.getElementById('focusMsgInput').addEventListener('keypress', (e) => {
            if(e.key === 'Enter') {
                let input = document.getElementById('focusMsgInput');
                if(input.value.trim()) {
                    addFocusInternalMessage(`💬 你：${input.value.trim()}`);
                    setTimeout(() => {
                        let reply = getSmartReply('focus_internal', input.value.trim());
                        addFocusInternalMessage(`💬 ${partnerData.name}：${reply}`);
                    }, 1000 + Math.random() * 2000);
                    input.value = '';
                }
            }
        });
    }

    function initPeriod() {
        let saved = JSON.parse(localStorage.getItem(STORAGE.period) || '{}');
        if(saved.lastDate) document.getElementById('periodLastDate').value = saved.lastDate;
        if(saved.cycle) document.getElementById('periodCycle').value = saved.cycle;
        document.getElementById('savePeriodBtn').onclick = () => {
            let lastDate = document.getElementById('periodLastDate').value;
            let cycle = parseInt(document.getElementById('periodCycle').value) || 28;
            if(!lastDate) { alert('请选择日期'); return; }
            localStorage.setItem(STORAGE.period, JSON.stringify({ lastDate, cycle }));
            updatePeriodInfo();
            addMessage('text', '🌙 已记录经期，我会提醒你注意身体的~', false);
        };
        updatePeriodInfo();
    }
    function updatePeriodInfo() {
        let saved = JSON.parse(localStorage.getItem(STORAGE.period) || '{}');
        let infoDiv = document.getElementById('periodInfo');
        if(!saved.lastDate) { infoDiv.style.display = 'none'; return; }
        let last = new Date(saved.lastDate);
        let cycle = saved.cycle || 28;
        let now = new Date();
        let diffDays = Math.floor((now - last) / (1000*60*60*24));
        let dayInCycle = diffDays % cycle;
        if(dayInCycle < 0) dayInCycle += cycle;
        let phase = '', daysLeft = 0, desc = '';
        if(dayInCycle <= 5) { phase = '月经期'; daysLeft = 6 - dayInCycle; desc = '注意保暖，多喝热水'; }
        else if(dayInCycle >= 10 && dayInCycle <= 16) { phase = '排卵期'; daysLeft = 17 - dayInCycle; desc = '排卵期，注意身体变化'; }
        else { phase = '安全期'; daysLeft = cycle - dayInCycle; desc = '安全期，放松心情'; }
        let nextPeriod = new Date(last); nextPeriod.setDate(last.getDate() + cycle);
        let countdown = Math.ceil((nextPeriod - now) / (1000*60*60*24));
        document.getElementById('periodPhase').innerText = `当前阶段：${phase}（还剩${daysLeft}天）`;
        document.getElementById('periodCountdown').innerText = `距离下次月经还有 ${countdown} 天\n💡 ${desc}`;
        infoDiv.style.display = 'block';
    }

    const GAMES = {
        guess: [
            { q: '猜猜我现在最想做什么？', options: ['抱抱你', '睡觉', '吃好吃的'], answer: 0, reply: '答对啦！最想抱抱你~', wrong: '不对哦，我最想抱抱你！' },
            { q: '猜猜我今天心情怎么样？', options: ['超级开心', '有点累', '想你了'], answer: 2, reply: '嘿嘿，就是想你了！', wrong: '不对，是想你了~' },
            { q: '猜猜我最喜欢你的什么？', options: ['眼睛', '全部', '性格'], answer: 1, reply: '当然是全部都喜欢啦！', wrong: '错啦，是全部都喜欢！' }
        ],
        truth: [
            { q: '和我在一起最开心的瞬间是什么？', reply: '只要和你在一起，每一秒都开心~' },
            { q: '你第一次心动是什么时候？', reply: '第一次见到你的时候，心跳漏了一拍。' },
            { q: '如果变成动物，你想变成什么陪在我身边？', reply: '想变成小猫，每天蹭你。' }
        ],
        quiz: [
            { q: '我们的纪念日是哪天？', options: ['不知道', '每个月都有', '你说了算'], answer: 1, reply: '和你在一起，每天都是纪念日！', wrong: '错啦，和你在一起的每一天都是纪念日~' },
            { q: '我最爱喝什么？', options: ['奶茶', '你喂的', '水'], answer: 1, reply: '你喂的我都爱喝~', wrong: '不对，是你喂的~' }
        ]
    };
    let currentGame = null, currentQuestion = null;
    function initGame() {
        document.querySelectorAll('.game-option').forEach(opt => {
            opt.onclick = () => {
                let type = opt.dataset.game;
                let questions = GAMES[type];
                let q = questions[Math.floor(Math.random() * questions.length)];
                currentGame = type;
                currentQuestion = q;
                document.getElementById('gameMenu').style.display = 'none';
                document.getElementById('gameArea').style.display = 'block';
                document.getElementById('gameQuestion').innerText = q.q;
                let optsDiv = document.getElementById('gameOptions');
                optsDiv.innerHTML = '';
                document.getElementById('gameResult').style.display = 'none';
                if(type === 'truth') {
                    let btn = document.createElement('button');
                    btn.className = 'game-btn'; btn.innerText = '查看答案';
                    btn.onclick = () => {
                        document.getElementById('gameResult').innerText = q.reply;
                        document.getElementById('gameResult').style.display = 'block';
                        addMessage('text', `💕 真心话答案：${q.reply}`, false);
                    };
                    optsDiv.appendChild(btn);
                } else {
                    q.options.forEach((optText, idx) => {
                        let btn = document.createElement('button');
                        btn.className = 'game-btn'; btn.innerText = optText;
                        btn.onclick = () => {
                            let isCorrect = idx === q.answer;
                            let result = isCorrect ? q.reply : q.wrong;
                            document.getElementById('gameResult').innerText = result;
                            document.getElementById('gameResult').style.display = 'block';
                            addMessage('text', `🎮 ${isCorrect ? '答对啦！' : '不对哦~'} ${result}`, false);
                        };
                        optsDiv.appendChild(btn);
                    });
                }
            };
        });
        document.getElementById('backGameBtn').onclick = () => {
            document.getElementById('gameMenu').style.display = 'block';
            document.getElementById('gameArea').style.display = 'none';
        };
    }

    function initWater() {
        let water = JSON.parse(localStorage.getItem(STORAGE.water) || '{"goal":8,"interval":2,"cups":0,"lastDrink":0,"lastRemind":0}');
        renderWaterCups(water);
        document.getElementById('drinkBtn').onclick = () => {
            water.cups = Math.min(water.cups + 1, 20);
            water.lastDrink = Date.now();
            localStorage.setItem(STORAGE.water, JSON.stringify(water));
            renderWaterCups(water);
            addMessage('text', `💧 我喝了一杯水（${water.cups}/${water.goal}）`, true);
            if(water.cups >= water.goal) {
                setTimeout(() => addMessage('text', '🎉 今日喝水目标达成！真棒~', false), 1000);
            }
        };
        document.getElementById('saveWaterBtn').onclick = () => {
            water.goal = parseInt(document.getElementById('waterGoal').value) || 8;
            water.interval = parseFloat(document.getElementById('waterInterval').value) || 2;
            localStorage.setItem(STORAGE.water, JSON.stringify(water));
            renderWaterCups(water);
            alert(`已保存：目标 ${water.goal} 杯，每 ${water.interval} 小时提醒`);
        };
        document.getElementById('waterGoal').value = water.goal;
        document.getElementById('waterInterval').value = water.interval;
    }
    function renderWaterCups(water) {
        document.getElementById('waterStatus').innerText = `今日已喝 ${water.cups} / ${water.goal} 杯`;
        let container = document.getElementById('waterCups');
        container.innerHTML = '';
        for(let i=0; i<<water.goal; i++) {
            let div = document.createElement('div');
            div.className = 'water-cup' + (i < water.cups ? ' filled' : '');
            div.innerText = i < water.cups ? '💧' : '🥛';
            container.appendChild(div);
        }
    }
    function checkWaterReminder() {
        let water = JSON.parse(localStorage.getItem(STORAGE.water) || '{"goal":8,"interval":2,"cups":0,"lastDrink":0,"lastRemind":0}');
        let now = Date.now();
        let intervalMs = water.interval * 3600000;
        if(water.cups < water.goal && (now - water.lastRemind > intervalMs) && (now - water.lastDrink > intervalMs)) {
            addMessage('text', `💧 该喝水啦！今天已经 ${water.cups}/${water.goal} 杯了，快去喝一杯~`, false);
            water.lastRemind = now;
            localStorage.setItem(STORAGE.water, JSON.stringify(water));
        }
    }

    function openFeaturePanel(panelId) {
        document.getElementById(panelId).classList.add('open');
        document.getElementById('overlay2').classList.add('show');
    }
    function closeFeaturePanel(panelId) {
        document.getElementById(panelId).classList.remove('open');
        let anyOpen = document.querySelectorAll('.feature-panel.open').length > 0;
        if(!anyOpen) document.getElementById('overlay2').classList.remove('show');
    }

    window.addEventListener('load', () => {
        applyTheme(currentTheme);
        applyBubbleStyle(currentBubbleStyle);
        applyFontStyle(currentFontStyle);
        
        document.getElementById('sendBtn').onclick = () => { 
            let input = document.getElementById('msgInput');
            if(input.value.trim()) sendUserMessage(input.value.trim()); 
            input.value=''; 
        };
        document.getElementById('msgInput').addEventListener('keypress', e=>{
            if(e.key==='Enter') {
                let input = document.getElementById('msgInput');
                if(input.value.trim()) sendUserMessage(input.value.trim()); 
                input.value=''; 
            }
        });
        document.getElementById('patBtn').onclick = () => sendPat(true);
        document.getElementById('emojiBtn').onclick = showEmojiPicker;
        document.getElementById('callBtn').onclick = startCall;
        
        let moreMenu = document.getElementById('moreMenu');
        document.getElementById('moreBtn').onclick = (e) => {
            e.stopPropagation();
            moreMenu.classList.toggle('show');
        };
        document.addEventListener('click', () => moreMenu.classList.remove('show'));
        
        document.getElementById('menuRedPacket').onclick = () => {
            moreMenu.classList.remove('show');
            sendRedPacket();
        };
        document.getElementById('menuTransfer').onclick = () => {
            moreMenu.classList.remove('show');
            sendTransfer();
        };
        document.getElementById('menuMusic').onclick = () => {
            moreMenu.classList.remove('show');
            document.getElementById('musicPanel').classList.add('open');
            document.getElementById('overlay2').classList.add('show');
        };
        
        document.getElementById('confirmTransferBtn').onclick = () => {
            let amount = parseFloat(document.getElementById('transferAmountInput').value);
            if(!amount || amount <= 0) { alert('请输入金额'); return; }
            if(amount > myBalance) { alert('余额不足！当前余额：' + myBalance.toFixed(2)); return; }
            myBalance -= amount;
            localStorage.setItem(STORAGE.balance + '_my', myBalance);
            addMessage('transfer', '', true, { amount, opened: false });
            document.getElementById('transferOverlay').classList.remove('show');
            document.getElementById('transferAmountInput').value = '';
            setTimeout(() => {
                addMessage('text', `💰 收到转账 ${amount}元`, false);
            }, 2000);
        };
        
        // 右上角 ⋯ 打开基础设置面板
        document.getElementById('menuSettingsBtn').onclick = () => { 
            document.getElementById('settingsPanel').classList.add('open'); 
            document.getElementById('overlay').classList.add('show'); 
            document.getElementById('partnerNicknameInput').value=partnerData.name; 
            document.getElementById('myNicknameInput').value=myData.name; 
            renderEmojiList(); 
            loadSettings(); 
        };
        document.getElementById('closeSettingsBtn').onclick = () => { 
            document.getElementById('settingsPanel').classList.remove('open'); 
            document.getElementById('overlay').classList.remove('show'); 
            let newPartnerName=document.getElementById('partnerNicknameInput').value.trim(); 
            if(newPartnerName && newPartnerName!==partnerData.name){ partnerData.name=newPartnerName; saveAll(); } 
            let newMyName=document.getElementById('myNicknameInput').value.trim(); 
            if(newMyName && newMyName!==myData.name){ myData.name=newMyName; saveAll(); renderMoments(); } 
            saveChatSettings(); 
        };
        document.getElementById('changePartnerAvatarBtn').onclick = () => changeAvatar('partner');
        document.getElementById('changeMyAvatarBtn').onclick = () => changeAvatar('my');
        document.getElementById('changeBgBtn').onclick = changeBackground;
        document.getElementById('changeMomentBgBtn').onclick = changeMomentBg;
        document.getElementById('changeCallBgBtn').onclick = changeCallBg;
        document.getElementById('addEmojiBtn').onclick = addEmojiFromFile;
        document.getElementById('overlay').onclick = () => { 
            document.getElementById('settingsPanel').classList.remove('open'); 
            document.getElementById('overlay').classList.remove('show'); 
        };
        document.querySelectorAll('.tab-item').forEach(tab=>tab.addEventListener('click',()=>switchTab(tab.dataset.tab)));
        
        // 设置Tab面板里的高级功能入口
        document.getElementById('openDressBtn2').onclick = () => { renderDressPanel(); openFeaturePanel('dressPanel'); };
        document.getElementById('openFortuneBtn2').onclick = () => { openFeaturePanel('fortunePanel'); };
        document.getElementById('openFocusBtn2').onclick = () => { 
            // 打开前刷新备注名和头像
            document.getElementById('focusMyName').innerText = myData.name;
            document.getElementById('focusPartnerName').innerText = partnerData.name;
            document.getElementById('focusChatLabelName').innerText = partnerData.name;
            document.getElementById('focusMyAvatar').innerHTML = myData.avatar.startsWith('data:image') ? `<img src="${myData.avatar}">` : myData.avatar;
            document.getElementById('focusPartnerAvatar').innerHTML = partnerData.avatar.startsWith('data:image') ? `<img src="${partnerData.avatar}">` : partnerData.avatar;
            openFeaturePanel('focusPanel'); 
        };
        document.getElementById('openPeriodBtn2').onclick = () => { initPeriod(); openFeaturePanel('periodPanel'); };
        document.getElementById('openGameBtn2').onclick = () => { openFeaturePanel('gamePanel'); };
        document.getElementById('openWaterBtn2').onclick = () => { initWater(); openFeaturePanel('waterPanel'); };
        
        // 各功能面板的关闭按钮
        document.getElementById('closeDressBtn').onclick = () => closeFeaturePanel('dressPanel');
        document.getElementById('closeFortuneBtn').onclick = () => closeFeaturePanel('fortunePanel');
        document.getElementById('closeFocusBtn').onclick = () => closeFeaturePanel('focusPanel');
        document.getElementById('closePeriodBtn').onclick = () => closeFeaturePanel('periodPanel');
        document.getElementById('closeGameBtn').onclick = () => closeFeaturePanel('gamePanel');
        document.getElementById('closeWaterBtn').onclick = () => closeFeaturePanel('waterPanel');
        
        document.getElementById('overlay2').onclick = () => {
            document.querySelectorAll('.feature-panel').forEach(p => p.classList.remove('open'));
            document.querySelectorAll('.music-panel').forEach(p => p.classList.remove('open'));
            document.getElementById('overlay2').classList.remove('show');
        };
        
        initFortune();
        initFocus();
        initGame();
        initMusic();
        
        applySettings();
        addMessage('text', '🐣 嗨 今天想聊什么', false);
        setTimeout(()=>addMessage('text', '🌸 长按消息可引用或撤回自己的消息', false), 1500);
        
        scheduleReplyCheck();
        scheduleActivePat();
        schedulePartnerMoment();
        schedulePartnerComment();
        schedulePartnerRedPacket();
        setInterval(checkWaterReminder, 60000);
        switchTab('chat');
        setTimeout(() => checkPendingReplies(), 1000);
    });
    
    function updateStatusTime() {
        let now=new Date(); 
        document.getElementById('statusTime').innerText=now.toLocaleTimeString('zh-CN',{hour12:false,hour:'2-digit',minute:'2-digit'});
        let year=now.getFullYear(), month=String(now.getMonth()+1).padStart(2,'0'), day=String(now.getDate()).padStart(2,'0'), hour=String(now.getHours()).padStart(2,'0'), minute=String(now.getMinutes()).padStart(2,'0');
        let dateDiv=document.getElementById('dateDivider'); 
        if(dateDiv) dateDiv.innerText=`${year}/${month}/${day} ${hour}:${minute}`;
    }
    setInterval(updateStatusTime,1000); 
    updateStatusTime();
</script>
</body>
</html>