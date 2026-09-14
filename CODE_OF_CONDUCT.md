available at [/**
 * LUCKYWIN52 Official Telegram Bot
 * Safe Customer Service / Information Version
 *
 * Script Properties:
 * BOT_TOKEN   = Telegram Bot Token
 * WEB_APP_URL = Apps Script Web App /exec URL
 *
 * Optional:
 * OFFICIAL_GROUP_URL = Official Telegram group/channel URL
 * ANNOUNCEMENT_TEXT  = Latest announcement
 */

const CONFIG = Object.freeze({
  BRAND: 'LUCKYWIN52',
  BOT_USERNAME: 'Luckywin52_bot',
  WEBSITE_URL: 'https://www.luckywin52.com',
  SUPPORT_USERNAME: 'Luckywin520000'
});


/* =========================
   CONFIG
========================= */

function getSettings() {
  const props = PropertiesService.getScriptProperties();

  const token = props.getProperty('BOT_TOKEN');

  if (!token) {
    throw new Error('BOT_TOKEN not found in Script Properties');
  }

  return {
    token: token,
    apiUrl: 'https://api.telegram.org/bot' + token,
    webAppUrl: props.getProperty('WEB_APP_URL') || '',
    groupUrl: props.getProperty('OFFICIAL_GROUP_URL') || '',
    announcement:
      props.getProperty('ANNOUNCEMENT_TEXT') ||
      '📢 暂时没有新的公告。'
  };
}


/* =========================
   WEB APP
========================= */

function doGet() {
  return ContentService
    .createTextOutput('LUCKYWIN52 Telegram Bot Running ✅')
    .setMimeType(ContentService.MimeType.TEXT);
}


function doPost(e) {
  try {

    if (!e || !e.postData || !e.postData.contents) {
      return ContentService.createTextOutput('NO DATA');
    }

    const update = JSON.parse(e.postData.contents);

    if (update.message) {
      handleMessage(update.message);
    }

    if (update.callback_query) {
      handleCallback(update.callback_query);
    }

    return ContentService.createTextOutput('OK');

  } catch (error) {

    console.error(error);

    return ContentService.createTextOutput('ERROR');
  }
}


/* =========================
   MESSAGE HANDLER
========================= */

function handleMessage(message) {

  const chatId = message.chat.id;
  const text = (message.text || '').trim();


  if (text === '/start') {
    sendLanguageMenu(chatId);
    return;
  }


  if (text === '/menu') {
    sendMainMenu(chatId, 'zh');
    return;
  }


  if (text === '/language') {
    sendLanguageMenu(chatId);
    return;
  }


  if (text === '/support') {
    sendSupport(chatId, 'zh');
    return;
  }


  if (text === '/help') {
    sendHelp(chatId, 'zh');
    return;
  }


  sendMessage(
    chatId,
    '👋 欢迎来到 LUCKYWIN52\n\n' +
    '请输入 /start 打开机器人菜单。'
  );
}


/* =========================
   LANGUAGE MENU
========================= */

function sendLanguageMenu(chatId) {

  const keyboard = {

    inline_keyboard: [

      [
        {
          text: '🇨🇳 中文',
          callback_data: 'LANG_ZH'
        },

        {
          text: '🇬🇧 English',
          callback_data: 'LANG_EN'
        }
      ],

      [
        {
          text: '🇲🇾 Bahasa Malaysia',
          callback_data: 'LANG_MS'
        }
      ]

    ]

  };


  sendMessage(
    chatId,
    '🌏 请选择语言\n\n' +
    'Please choose your language\n\n' +
    'Sila pilih bahasa',
    keyboard
  );
}


/* =========================
   MAIN MENU
========================= */

function sendMainMenu(chatId, lang) {

  const settings = getSettings();


  const text = {

    zh:
      '🎉 欢迎来到 LUCKYWIN52\n\n' +
      '请选择您需要的服务：',

    en:
      '🎉 Welcome to LUCKYWIN52\n\n' +
      'Please choose a service:',

    ms:
      '🎉 Selamat datang ke LUCKYWIN52\n\n' +
      'Sila pilih perkhidmatan:'

  };


  const buttons = {

    zh: {
      website: '🌐 官网资讯',
      group: '👥 官方资讯群',
      announcement: '📢 最新公告',
      support: '👨‍💼 在线客服',
      faq: '❓ 常见问题',
      share: '📤 分享机器人',
      language: '🌏 切换语言'
    },

    en: {
      website: '🌐 Official Website',
      group: '👥 Official Community',
      announcement: '📢 Latest Announcement',
      support: '👨‍💼 Customer Support',
      faq: '❓ FAQ',
      share: '📤 Share Bot',
      language: '🌏 Change Language'
    },

    ms: {
      website: '🌐 Laman Rasmi',
      group: '👥 Komuniti Rasmi',
      announcement: '📢 Pengumuman Terkini',
      support: '👨‍💼 Khidmat Pelanggan',
      faq: '❓ Soalan Lazim',
      share: '📤 Kongsi Bot',
      language: '🌏 Tukar Bahasa'
    }

  };


  const b = buttons[lang] || buttons.zh;


  const keyboard = {

    inline_keyboard: [

      [
        {
          text: b.website,
          url: CONFIG.WEBSITE_URL
        }
      ],

      settings.groupUrl
        ? [
            {
              text: b.group,
              url: settings.groupUrl
            }
          ]
        : [
            {
              text: b.group,
              callback_data: 'GROUP_' + lang.toUpperCase()
            }
          ],

      [
        {
          text: b.announcement,
          callback_data: 'ANN_' + lang.toUpperCase()
        }
      ],

      [
        {
          text: b.support,
          url: 'https://t.me/' + CONFIG.SUPPORT_USERNAME
        }
      ],

      [
        {
          text: b.faq,
          callback_data: 'FAQ_' + lang.toUpperCase()
        }
      ],

      [
        {
          text: b.share,
          callback_data: 'SHARE_' + lang.toUpperCase()
        }
      ],

      [
        {
          text: b.language,
          callback_data: 'LANG_MENU'
        }
      ]

    ]

  };


  sendMessage(
    chatId,
    text[lang] || text.zh,
    keyboard
  );
}


/* =========================
   CALLBACKS
========================= */

function handleCallback(callback) {

  const chatId = callback.message.chat.id;
  const data = callback.data || '';

  answerCallback(callback.id);


  if (data === 'LANG_ZH') {
    sendMainMenu(chatId, 'zh');
    return;
  }


  if (data === 'LANG_EN') {
    sendMainMenu(chatId, 'en');
    return;
  }


  if (data === 'LANG_MS') {
    sendMainMenu(chatId, 'ms');
    return;
  }


  if (data === 'LANG_MENU') {
    sendLanguageMenu(chatId);
    return;
  }


  if (data === 'ANN_ZH') {
    sendAnnouncement(chatId, 'zh');
    return;
  }


  if (data === 'ANN_EN') {
    sendAnnouncement(chatId, 'en');
    return;
  }


  if (data === 'ANN_MS') {
    sendAnnouncement(chatId, 'ms');
    return;
  }


  if (data === 'FAQ_ZH') {
    sendHelp(chatId, 'zh');
    return;
  }


  if (data === 'FAQ_EN') {
    sendHelp(chatId, 'en');
    return;
  }


  if (data === 'FAQ_MS') {
    sendHelp(chatId, 'ms');
    return;
  }


  if (data === 'SHARE_ZH') {
    sendShare(chatId, 'zh');
    return;
  }


  if (data === 'SHARE_EN') {
    sendShare(chatId, 'en');
    return;
  }


  if (data === 'SHARE_MS') {
    sendShare(chatId, 'ms');
    return;
  }


  if (data.indexOf('GROUP_') === 0) {
    sendGroupInfo(chatId);
    return;
  }
}


/* =========================
   ANNOUNCEMENT
========================= */

function sendAnnouncement(chatId, lang) {

  const settings = getSettings();


  const title = {

    zh: '📢 最新公告\n\n',

    en: '📢 Latest Announcement\n\n',

    ms: '📢 Pengumuman Terkini\n\n'

  };


  sendMessage(
    chatId,
    (title[lang] || title.zh) +
    settings.announcement
  );
}


/* =========================
   CUSTOMER SUPPORT
========================= */

function sendSupport(chatId, lang) {

  const text = {

    zh:
      '👨‍💼 在线客服\n\n' +
      '@' + CONFIG.SUPPORT_USERNAME,

    en:
      '👨‍💼 Customer Support\n\n' +
      '@' + CONFIG.SUPPORT_USERNAME,

    ms:
      '👨‍💼 Khidmat Pelanggan\n\n' +
      '@' + CONFIG.SUPPORT_USERNAME

  };


  const keyboard = {

    inline_keyboard: [

      [
        {
          text: '👨‍💼 Open Support',
          url: 'https://t.me/' + CONFIG.SUPPORT_USERNAME
        }
      ]

    ]

  };


  sendMessage(
    chatId,
    text[lang] || text.zh,
    keyboard
  );
}


/* =========================
   FAQ
========================= */

function sendHelp(chatId, lang) {

  const text = {

    zh:
      '❓ 常见问题\n\n' +
      '• 如何联系客服？\n' +
      '点击「在线客服」按钮。\n\n' +
      '• 如何查看公告？\n' +
      '点击「最新公告」。\n\n' +
      '• 如何切换语言？\n' +
      '点击「切换语言」。\n\n' +
      '• 技术问题怎么办？\n' +
      '请直接联系客服。',

    en:
      '❓ FAQ\n\n' +
      '• How do I contact support?\n' +
      'Tap Customer Support.\n\n' +
      '• Where can I see announcements?\n' +
      'Tap Latest Announcement.\n\n' +
      '• How do I change language?\n' +
      'Tap Change Language.\n\n' +
      '• Technical issue?\n' +
      'Please contact support.',

    ms:
      '❓ Soalan Lazim\n\n' +
      '• Bagaimana hubungi khidmat pelanggan?\n' +
      'Tekan Khidmat Pelanggan.\n\n' +
      '• Di mana lihat pengumuman?\n' +
      'Tekan Pengumuman Terkini.\n\n' +
      '• Bagaimana tukar bahasa?\n' +
      'Tekan Tukar Bahasa.\n\n' +
      '• Ada masalah teknikal?\n' +
      'Sila hubungi khidmat pelanggan.'

  };


  sendMessage(
    chatId,
    text[lang] || text.zh
  );
}


/* =========================
   SHARE BOT
========================= */

function sendShare(chatId, lang) {

  const botUrl =
    'https://t.me/' + CONFIG.BOT_USERNAME;


  const text = {

    zh:
      '📤 分享 LUCKYWIN52 Bot\n\n' +
      botUrl,

    en:
      '📤 Share LUCKYWIN52 Bot\n\n' +
      botUrl,

    ms:
      '📤 Kongsi LUCKYWIN52 Bot\n\n' +
      botUrl

  };


  const shareUrl =
    'https://t.me/share/url?url=' +
    encodeURIComponent(botUrl) +
    '&text=' +
    encodeURIComponent('LUCKYWIN52 Official');


  const keyboard = {

    inline_keyboard: [

      [
        {
          text: '📤 Share',
          url: shareUrl
        }
      ]

    ]

  };


  sendMessage(
    chatId,
    text[lang] || text.zh,
    keyboard
  );
}


/* =========================
   GROUP INFO
========================= */

function sendGroupInfo(chatId) {

  sendMessage(
    chatId,
    '👥 Official Community\n\n' +
    'OFFICIAL_GROUP_URL 还没有设定。\n\n' +
    '请在 Script Properties 加入官方群链接。'
  );
}


/* =========================
   TELEGRAM API
========================= */

function sendMessage(chatId, text, replyMarkup) {

  const settings = getSettings();


  const payload = {

    chat_id: chatId,

    text: text,

    disable_web_page_preview: true

  };


  if (replyMarkup) {

    payload.reply_markup =
      replyMarkup;

  }


  const response = UrlFetchApp.fetch(

    settings.apiUrl + '/sendMessage',

    {

      method: 'post',

      contentType: 'application/json',

      payload: JSON.stringify(payload),

      muteHttpExceptions: true

    }

  );


  return response.getContentText();
}


/* =========================
   CALLBACK ACK
========================= */

function answerCallback(callbackId) {

  const settings = getSettings();


  UrlFetchApp.fetch(

    settings.apiUrl +
    '/answerCallbackQuery',

    {

      method: 'post',

      contentType: 'application/json',

      payload: JSON.stringify({

        callback_query_id: callbackId

      }),

      muteHttpExceptions: true

    }

  );
}


/* =========================
   WEBHOOK SETUP
========================= */

function setWebhook() {

  const settings = getSettings();


  if (!settings.webAppUrl) {

    throw new Error(
      'WEB_APP_URL not found in Script Properties'
    );

  }


  const response =
    UrlFetchApp.fetch(

      settings.apiUrl +
      '/setWebhook?url=' +
      encodeURIComponent(
        settings.webAppUrl
      )

    );


  Logger.log(
    response.getContentText()
  );


  return response.getContentText();
}


/* =========================
   WEBHOOK INFO
========================= */

function getWebhookInfo() {

  const settings = getSettings();


  const response =
    UrlFetchApp.fetch(

      settings.apiUrl +
      '/getWebhookInfo'

    );


  Logger.log(
    response.getContentText()
  );


  return response.getContentText();
}


/* =========================
   DELETE WEBHOOK
========================= */

function deleteWebhook() {

  const settings = getSettings();


  const response =
    UrlFetchApp.fetch(

      settings.apiUrl +
      '/deleteWebhook'

    );


  Logger.log(
    response.getContentText()
  );


  return response.getContentText();
}


/* =========================
   TEST BOT
========================= */

function testBot() {

  const settings = getSettings();


  const response =
    UrlFetchApp.fetch(

      settings.apiUrl +
      '/getMe'

    );


  Logger.log(
    response.getContentText()
  );


  return response.getContentText();
}](http://contributor-covenant.org/version/1/2/0/)
