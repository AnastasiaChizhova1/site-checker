<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Проверка сайта</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background: linear-gradient(to bottom, #1e1e2f, #4a90e2);
            color: white;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            font-family: Arial, sans-serif;
            position: relative;
            overflow-x: hidden;
            text-align: center;
            padding: 20px;
        }

        body::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle, rgba(255, 255, 255, 0.1) 1px, transparent 1px);
            background-size: 50px 50px;
            animation: starTwinkle 10s infinite linear;
            z-index: -1;
        }

        @keyframes starTwinkle {
            0% { background-position: 0 0; }
            100% { background-position: 100px 100px; }
        }

        .form-control, .btn {
            max-width: 500px;
            margin: 10px auto;
        }

        .result {
            margin-top: 20px;
            font-size: 0.9rem;
            max-height: 400px;
            overflow-y: auto;
            background: rgba(0, 0, 0, 0.5);
            padding: 10px;
            border-radius: 10px;
            line-height: 1.2;
            text-align: left;
            max-width: 500px;
            margin-left: auto;
            margin-right: auto;
        }

        .progress {
            margin-top: 20px;
            max-width: 500px;
            margin-left: auto;
            margin-right: auto;
        }

        .language-selector {
            max-width: 200px;
            margin: 10px auto;
        }

        footer {
            background: rgba(0, 0, 0, 0.5);
            padding: 10px;
            border-radius: 10px;
            margin-top: 20px;
            font-size: 0.9rem;
        }

        footer a {
            color: #4caf50;
            text-decoration: none;
        }

        footer a:hover {
            text-decoration: underline;
        }

        .explanation {
            font-size: 0.8rem;
            color: #b0b0b0;
            margin-top: 5px;
            font-style: italic;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="mb-4" id="title">Проверка сайта</h1>
        <select id="languageSelector" class="form-select language-selector" onchange="changeLanguage()">
            <option value="ru" selected>Русский</option>
            <option value="en">English</option>
            <option value="uk">Українська</option>
        </select>
        <input type="text" id="urlInput" class="form-control" placeholder="Введите URL (например, https://example.com)">
        <button onclick="checkSite()" class="btn btn-light" id="checkButton">Проверить</button>
        <div class="progress" id="progressBar" style="display: none;">
            <div class="progress-bar progress-bar-striped progress-bar-animated" role="progressbar" style="width: 0%;" id="progressFill">0%</div>
        </div>
        <div id="result" class="result"></div>
    </div>

    <footer>
        <p>© 2025 | Created by Anastasia Chizhova</p>
        <p>
            Email: <a href="mailto:anastasiiachizhova98@gmail.com">anastasiiachizhova98@gmail.com</a> | 
            Telegram: <a href="https://t.me/chizhik15">@chizhik15</a> | 
            LinkedIn: <a href="https://www.linkedin.com/in/anastasia-chizhova-7055b1143">anastasia-chizhova</a>
        </p>
    </footer>

    <script>
        const translations = {
            ru: {
                title: "Проверка сайта",
                placeholder: "Введите URL (например, https://example.com)",
                checkButton: "Проверить",
                noUrl: "❗️ Введите URL",
                invalidUrl: "❌ Неверный URL",
                checking: "🔄 Проверка...",
                tests: {
                    accessibility: "Доступность",
                    https: "HTTPS",
                    loadTime: "Скорость загрузки",
                    urlFormat: "Формат URL",
                    urlLength: "Длина URL",
                    redirects: "Редиректы",
                    protocol: "Наличие протокола",
                    topLevelDomain: "Домен верхнего уровня",
                    subdomains: "Поддомены",
                    cyrillic: "Кириллица в URL",
                    parameters: "Параметры в URL",
                    slashes: "Слеши в пути",
                    uppercase: "Заглавные буквы",
                    keywords: "Ключевые слова",
                    ipAddress: "IPv4/IPv6",
                    port: "Порт в URL",
                    encoding: "Кодирование символов",
                    hyphens: "Дефисы в URL",
                    anchor: "Якорные ссылки",
                    suspicious: "Подозрительные символы",
                    w3c: "Стандарты W3C",
                    httpStatus: "Статус HTTP-запроса",
                    metaTags: "Мета-теги",
                    iotCompatibility: "IoT-совместимость"
                },
                explanations: {
                    accessibility: "Проверяем, можно ли зайти на сайт. Если он отвечает, значит, всё работает, и люди могут его открыть.",
                    https: "Проверяем, защищён ли сайт. HTTPS — это как замок, который защищает твои данные (например, пароли) от злоумышленников.",
                    loadTime: "Измеряем, как быстро открывается сайт. Быстрая загрузка важна, чтобы людям не пришлось долго ждать.",
                    urlFormat: "Проверяем, правильно ли написан адрес сайта. Если он неправильный, браузер не сможет открыть страницу.",
                    urlLength: "Смотрим, не слишком ли длинный адрес. Длинные адреса неудобны и могут плохо работать в поисковиках, например, в Google.",
                    redirects: "Проверяем, не перебрасывает ли сайт на другой адрес. Слишком много таких перебросов может запутать и замедлить загрузку.",
                    protocol: "Смотрим, есть ли в начале адреса 'http://' или 'https://'. Без этого браузер может не понять, как открыть сайт.",
                    topLevelDomain: "Проверяем, есть ли у сайта правильный 'хвостик', например, '.ru' или '.com'. Без него адрес может быть недействительным.",
                    subdomains: "Смотрим, есть ли у сайта поддомены, например, 'spb' в 'spb.h.ru'. Они помогают разделить сайт на части, например, для разных городов.",
                    cyrillic: "Проверяем, есть ли в адресе русские буквы. Они могут вызывать проблемы в старых браузерах или при копировании.",
                    parameters: "Смотрим, есть ли в адресе дополнительные части (например, '?category=news'). Они нужны, чтобы сайт показывал разные данные.",
                    slashes: "Считаем, сколько 'слешей' ('/') в адресе, например, '/blog/post'. Если их слишком много, адрес может быть сложным.",
                    uppercase: "Проверяем, есть ли в адресе заглавные буквы. Лучше их избегать, чтобы не запутать людей.",
                    keywords: "Ищем в адресе слова, которые подсказывают, о чём сайт (например, 'blog'). Это помогает поисковикам, таким как Google.",
                    ipAddress: "Проверяем, не используется ли числовой адрес (например, '192.168.0.1') вместо обычного. Это необычно для сайтов.",
                    port: "Смотрим, указан ли порт, например, ':8080'. Обычно он не нужен, потому что используется стандартный.",
                    encoding: "Проверяем, есть ли в адресе закодированные символы, например, '%20' (пробел). Это нормально, но лучше, чтобы адрес был простым.",
                    hyphens: "Считаем дефисы ('-') в адресе. Их не должно быть слишком много, иначе адрес выглядит странно.",
                    anchor: "Смотрим, есть ли в адресе 'якорь' (например, '#intro'). Он указывает на конкретное место на странице, что удобно.",
                    suspicious: "Проверяем, нет ли в адресе странных символов, например, '<' или '>'. Они могут быть опасны и использоваться хакерами.",
                    w3c: "Проверяем, соответствует ли адрес правилам интернета (W3C). Например, в нём не должно быть пробелов или странных символов.",
                    httpStatus: "Проверяем код ответа сервера (например, 200 OK или 404 Not Found). Это показывает, работает ли сайт корректно.",
                    metaTags: "Проверяем наличие и качество заголовков страницы (title, description). Они важны для поисковиков, таких как Google.",
                    iotCompatibility: "Проверяем, поддерживает ли сайт интеграцию с IoT-устройствами через API или WebSocket. Это важно для современных веб-приложений."
                },
                messages: {
                    accessible: "Доступен",
                    notAccessible: "Недоступен",
                    httpsUsed: "Использует HTTPS",
                    httpUsed: "Использует HTTP",
                    loadTime: (time) => `${time}с ${time < 2 ? "(быстро)" : "(медленно)"}`,
                    loadTimeFailed: "Не удалось измерить",
                    urlValid: "Корректен",
                    urlInvalid: "Некорректен",
                    urlLength: (len) => `${len} симв. ${len <= 200 ? "(ОК)" : "(длинный)"}`,
                    noRedirects: "Нет",
                    hasRedirects: (status) => `Есть (${status})`,
                    redirectsFailed: "Не удалось проверить",
                    protocolPresent: "Есть",
                    protocolMissing: "Нет",
                    tldValid: "Корректен",
                    tldInvalid: "Некорректен",
                    subdomainsPresent: "Есть",
                    subdomainsMissing: "Нет",
                    cyrillicPresent: "Есть",
                    cyrillicMissing: "Нет",
                    paramsPresent: "Есть",
                    paramsMissing: "Нет",
                    slashes: (count) => `Слешей: ${count} ${count <= 5 ? "(ОК)" : "(много)"}`,
                    uppercasePresent: "Есть",
                    uppercaseMissing: "Нет",
                    keywordsPresent: "Есть",
                    keywordsMissing: "Нет",
                    ipPresent: "Есть",
                    ipMissing: "Нет",
                    portPresent: "Есть",
                    portMissing: "Нет",
                    encodingPresent: "Есть",
                    encodingMissing: "Нет",
                    hyphens: (count) => `Дефисов: ${count} ${count <= 3 ? "(ОК)" : "(много)"}`,
                    anchorPresent: "Есть",
                    anchorMissing: "Нет",
                    suspiciousPresent: "Есть",
                    suspiciousMissing: "Нет",
                    w3cCompliant: "Соответствует",
                    w3cNonCompliant: "Не соответствует",
                    httpStatus: (code) => `Код: ${code} ${code === 200 ? "(OK)" : "(ошибка)"}`,
                    httpStatusFailed: "Не удалось проверить",
                    metaTagsPresent: "Присутствуют",
                    metaTagsMissing: "Отсутствуют",
                    iotSupported: "Поддерживается",
                    iotNotSupported: "Не поддерживается"
                },
                success: "✅ Успешно",
                error: "⚠️ Проблемы"
            },
            en: {
                title: "Website Check",
                placeholder: "Enter URL (e.g., https://example.com)",
                checkButton: "Check",
                noUrl: "❗️ Please enter a URL",
                invalidUrl: "❌ Invalid URL format",
                checking: "🔄 Checking...",
                tests: {
                    accessibility: "Accessibility",
                    https: "HTTPS",
                    loadTime: "Load Time",
                    urlFormat: "URL Format",
                    urlLength: "URL Length",
                    redirects: "Redirects",
                    protocol: "Protocol Presence",
                    topLevelDomain: "Top-Level Domain",
                    subdomains: "Subdomains",
                    cyrillic: "Cyrillic in URL",
                    parameters: "URL Parameters",
                    slashes: "Slashes in Path",
                    uppercase: "Uppercase Letters",
                    keywords: "Keywords in URL",
                    ipAddress: "IPv4/IPv6",
                    port: "Port in URL",
                    encoding: "Encoded Characters",
                    hyphens: "Hyphens in URL",
                    anchor: "Anchor Links",
                    suspicious: "Suspicious Characters",
                    w3c: "W3C Standards",
                    httpStatus: "HTTP Status",
                    metaTags: "Meta Tags",
                    iotCompatibility: "IoT Compatibility"
                },
                explanations: {
                    accessibility: "We check if the website is reachable. If it responds, it means it works and people can visit it.",
                    https: "We check if the site is secure. HTTPS is like a lock that keeps your data (like passwords) safe from hackers.",
                    loadTime: "We measure how fast the site loads. A fast site is important so people don’t have to wait too long.",
                    urlFormat: "We check if the site’s address is written correctly. If it’s wrong, the browser can’t open the page.",
                    urlLength: "We look at how long the address is. Long addresses can be inconvenient and may not work well in search engines like Google.",
                    redirects: "We check if the site redirects to another address. Too many redirects can confuse users and slow things down.",
                    protocol: "We check if the address starts with 'http://' or 'https://'. Without this, the browser might not know how to open the site.",
                    topLevelDomain: "We check if the site has a proper ending, like '.com' or '.ru'. Without it, the address might not work.",
                    subdomains: "We look for subdomains, like 'spb' in 'spb.h.ru'. They help organize the site, for example, for different cities.",
                    cyrillic: "We check if the address has Russian letters. They can cause issues in older browsers or when copying the link.",
                    parameters: "We look for extra parts in the address (like '?category=news'). They help the site show different information.",
                    slashes: "We count the 'slashes' ('/') in the address, like '/blog/post'. Too many can make the address complicated.",
                    uppercase: "We check for uppercase letters in the address. It’s better to avoid them to prevent confusion.",
                    keywords: "We look for words in the address that halo at the site’s content (like 'blog'). This helps search engines like Google.",
                    ipAddress: "We check if the address uses a number (like '192.168.0.1') instead of a name. This is unusual for regular sites.",
                    port: "We check if a port is specified, like ':8080'. Normally, it’s not needed because the default one is used.",
                    encoding: "We check for encoded characters in the address, like '%20' (a space). It’s okay, but simple addresses are better.",
                    hyphens: "We count hyphens ('-') in the address. Too many can make the address look odd.",
                    anchor: "We check for an 'anchor' (like '#intro') in the address. It points to a specific spot on the page, which is handy.",
                    suspicious: "We look for strange characters, like '<' or '>'. These can be dangerous and used by hackers.",
                    w3c: "We check if the address follows internet rules (W3C). For example, it shouldn’t have spaces or weird symbols.",
                    httpStatus: "Checks the server response code (e.g., 200 OK or 404 Not Found). This shows if the site works correctly.",
                    metaTags: "Checks the presence and quality of page headers (title, description). They are crucial for search engines like Google.",
                    iotCompatibility: "Checks if the site supports IoT device integration via API or WebSocket. This is important for modern web applications."
                },
                messages: {
                    accessible: "Accessible",
                    notAccessible: "Not accessible",
                    httpsUsed: "Uses HTTPS",
                    httpUsed: "Uses HTTP",
                    loadTime: (time) => `${time}s ${time < 2 ? "(fast)" : "(slow)"}`,
                    loadTimeFailed: "Failed to measure",
                    urlValid: "Valid",
                    urlInvalid: "Invalid",
                    urlLength: (len) => `${len} chars ${len <= 200 ? "(OK)" : "(long)"}`,
                    noRedirects: "None",
                    hasRedirects: (status) => `Found (${status})`,
                    redirectsFailed: "Failed to check",
                    protocolPresent: "Present",
                    protocolMissing: "Missing",
                    tldValid: "Valid",
                    tldInvalid: "Invalid",
                    subdomainsPresent: "Present",
                    subdomainsMissing: "Missing",
                    cyrillicPresent: "Present",
                    cyrillicMissing: "Missing",
                    paramsPresent: "Present",
                    paramsMissing: "Missing",
                    slashes: (count) => `Slashes: ${count} ${count <= 5 ? "(OK)" : "(too many)"}`,
                    uppercasePresent: "Present",
                    uppercaseMissing: "Missing",
                    keywordsPresent: "Present",
                    keywordsMissing: "Missing",
                    ipPresent: "Present",
                    ipMissing: "Missing",
                    portPresent: "Present",
                    portMissing: "Missing",
                    encodingPresent: "Present",
                    encodingMissing: "Missing",
                    hyphens: (count) => `Hyphens: ${count} ${count <= 3 ? "(OK)" : "(too many)"}`,
                    anchorPresent: "Present",
                    anchorMissing: "Missing",
                    suspiciousPresent: "Present",
                    suspiciousMissing: "Missing",
                    w3cCompliant: "Compliant",
                    w3cNonCompliant: "Non-compliant",
                    httpStatus: (code) => `Code: ${code} ${code === 200 ? "(OK)" : "(error)"}`,
                    httpStatusFailed: "Failed to check",
                    metaTagsPresent: "Present",
                    metaTagsMissing: "Missing",
                    iotSupported: "Supported",
                    iotNotSupported: "Not supported"
                },
                success: "✅ Success",
                error: "⚠️ Issues"
            },
            uk: {
                title: "Перевірка сайту",
                placeholder: "Введіть URL (наприклад, https://example.com)",
                checkButton: "Перевірити",
                noUrl: "❗️ Введіть URL",
                invalidUrl: "❌ Неправильний формат URL",
                checking: "🔄 Перевірка...",
                tests: {
                    accessibility: "Доступність",
                    https: "HTTPS",
                    loadTime: "Швидкість завантаження",
                    urlFormat: "Формат URL",
                    urlLength: "Довжина URL",
                    redirects: "Редиректи",
                    protocol: "Наявність протоколу",
                    topLevelDomain: "Домен верхнього рівня",
                    subdomains: "Піддомени",
                    cyrillic: "Кирилиця в URL",
                    parameters: "Параметри в URL",
                    slashes: "Слеші в шляху",
                    uppercase: "Великі літери",
                    keywords: "Ключові слова",
                    ipAddress: "IPv4/IPv6",
                    port: "Порт в URL",
                    encoding: "Кодування символів",
                    hyphens: "Дефіси в URL",
                    anchor: "Якірні посилання",
                    suspicious: "Підозрілі символи",
                    w3c: "Стандарти W3C",
                    httpStatus: "Статус HTTP-запиту",
                    metaTags: "Мета-теги",
                    iotCompatibility: "IoT-сумісність"
                },
                explanations: {
                    accessibility: "Перевіряємо, чи можна зайти на сайт. Якщо він відповідає, значить, працює, і люди можуть його відкрити.",
                    https: "Перевіряємо, чи захищений сайт. HTTPS — це як замок, який захищає твої дані (наприклад, паролі) від зловмисників.",
                    loadTime: "Вимірюємо, як швидко відкривається сайт. Швидке завантаження важливе, щоб людям не довелося довго чекати.",
                    urlFormat: "Перевіряємо, чи правильно написана адреса сайту. Якщо вона неправильна, браузер не зможе відкрити сторінку.",
                    urlLength: "Дивимося, чи не занадто довга адреса. Довгі адреси незручні і можуть погано працювати в пошуковиках, наприклад, у Google.",
                    redirects: "Перевіряємо, чи не перекидає сайт на іншу адресу. Занадто багато таких перекидів може заплутати і сповільнити завантаження.",
                    protocol: "Дивимося, чи є на початку адреси 'http://' або 'https://'. Без цього браузер може не зрозуміти, як відкрити сайт.",
                    topLevelDomain: "Перевіряємо, чи є у сайту правильний 'хвостик', наприклад, '.ru' або '.com'. Без нього адреса може бути недійсною.",
                    subdomains: "Дивимося, чи є у сайту піддомени, наприклад, 'spb' у 'spb.h.ru'. Вони допомагають розділити сайт на частини, наприклад, для різних міст.",
                    cyrillic: "Перевіряємо, чи є в адресі українські або російські літери. Вони можуть викликати проблеми в старих браузерах або при копіюванні.",
                    parameters: "Дивимося, чи є в адресі додаткові частини (наприклад, '?category=news'). Вони потрібні, щоб сайт показував різні дані.",
                    slashes: "Рахуємо, скільки 'слешів' ('/') в адресі, наприклад, '/blog/post'. Якщо їх забагато, адреса може бути складною.",
                    uppercase: "Перевіряємо, чи є в адресі великі літери. Краще їх уникати, щоб не заплутати людей.",
                    keywords: "Шукаємо в адресі слова, які підказують, про що сайт (наприклад, 'blog'). Це допомагає пошуковикам, таким як Google.",
                    ipAddress: "Перевіряємо, чи не використовується числова адреса (наприклад, '192.168.0.1') замість звичайної. Це незвично для сайтів.",
                    port: "Дивимося, чи вказано порт, наприклад, ':8080'. Зазвичай він не потрібен, бо використовується стандартний.",
                    encoding: "Перевіряємо, чи є в адресі закодовані символи, наприклад, '%20' (пробіл). Це нормально, але краще, щоб адреса була простою.",
                    hyphens: "Рахуємо дефіси ('-') в адресі. Їх не повинно бути забагато, інакше адреса виглядає дивно.",
                    anchor: "Дивимося, чи є в адресі 'якір' (наприклад, '#intro'). Він вказує на конкретне місце на сторінці, що зручно.",
                    suspicious: "Перевіряємо, чи немає в адресі дивних символів, наприклад, '<' або '>'. Вони можуть бути небезпечними і використовуватися хакерами.",
                    w3c: "Перевіряємо, чи відповідає адреса правилам інтернету (W3C). Наприклад, у ній не повинно бути пробілів або дивних символів.",
                    httpStatus: "Перевіряємо код відповіді сервера (наприклад, 200 OK або 404 Not Found). Це показує, чи працює сайт коректно.",
                    metaTags: "Перевіряємо наявність і якість заголовків сторінки (title, description). Вони важливі для пошуковиків, таких як Google.",
                    iotCompatibility: "Перевіряємо, чи підтримує сайт інтеграцію з IoT-пристроями через API або WebSocket. Це важливо для сучасних веб-додатків."
                },
                messages: {
                    accessible: "Доступний",
                    notAccessible: "Недоступний",
                    httpsUsed: "Використовує HTTPS",
                    httpUsed: "Використовує HTTP",
                    loadTime: (time) => `${time}с ${time < 2 ? "(швидко)" : "(повільно)"}`,
                    loadTimeFailed: "Не вдалося виміряти",
                    urlValid: "Коректний",
                    urlInvalid: "Некоректний",
                    urlLength: (len) => `${len} симв. ${len <= 200 ? "(ОК)" : "(довгий)"}`,
                    noRedirects: "Немає",
                    hasRedirects: (status) => `Є (${status})`,
                    redirectsFailed: "Не вдалося перевірити",
                    protocolPresent: "Є",
                    protocolMissing: "Немає",
                    tldValid: "Коректний",
                    tldInvalid: "Некоректний",
                    subdomainsPresent: "Є",
                    subdomainsMissing: "Немає",
                    cyrillicPresent: "Є",
                    cyrillicMissing: "Немає",
                    paramsPresent: "Є",
                    paramsMissing: "Немає",
                    slashes: (count) => `Слешів: ${count} ${count <= 5 ? "(ОК)" : "(забагато)"}`,
                    uppercasePresent: "Є",
                    uppercaseMissing: "Немає",
                    keywordsPresent: "Є",
                    keywordsMissing: "Немає",
                    ipPresent: "Є",
                    ipMissing: "Немає",
                    portPresent: "Є",
                    portMissing: "Немає",
                    encodingPresent: "Є",
                    encodingMissing: "Немає",
                    hyphens: (count) => `Дефісів: ${count} ${count <= 3 ? "(ОК)" : "(забагато)"}`,
                    anchorPresent: "Є",
                    anchorMissing: "Немає",
                    suspiciousPresent: "Є",
                    suspiciousMissing: "Немає",
                    w3cCompliant: "Відповідає",
                    w3cNonCompliant: "Не відповідає",
                    httpStatus: (code) => `Код: ${code} ${code === 200 ? "(OK)" : "(помилка)"}`,
                    httpStatusFailed: "Не вдалося перевірити",
                    metaTagsPresent: "Присутні",
                    metaTagsMissing: "Відсутні",
                    iotSupported: "Підтримується",
                    iotNotSupported: "Не підтримується"
                },
                success: "✅ Успішно",
                error: "⚠️ Проблеми"
            }
        };

        let currentLang = 'ru';

        function changeLanguage() {
            currentLang = document.getElementById('languageSelector').value;
            document.getElementById('title').textContent = translations[currentLang].title;
            document.getElementById('urlInput').placeholder = translations[currentLang].placeholder;
            document.getElementById('checkButton').textContent = translations[currentLang].checkButton;
            document.getElementById('result').innerHTML = '';
        }

        async function checkSite() {
            const url = document.getElementById("urlInput").value.trim();
            const resultDiv = document.getElementById("result");
            const progressBar = document.getElementById("progressBar");
            const progressFill = document.getElementById("progressFill");

            if (!url) {
                resultDiv.innerHTML = translations[currentLang].noUrl;
                return;
            }

            let parsedUrl;
            try {
                parsedUrl = new URL(url);
            } catch {
                resultDiv.innerHTML = translations[currentLang].invalidUrl;
                return;
            }

            progressBar.style.display = 'block';
            resultDiv.innerHTML = translations[currentLang].checking;

            const totalTests = 24; // Было 21, добавлено 3 новых теста
            let completedTests = 0;
            let results = [];

            function updateProgress() {
                completedTests++;
                const progressPercent = (completedTests / totalTests) * 100;
                progressFill.style.width = `${progressPercent}%`;
                progressFill.textContent = `${Math.round(progressPercent)}%`;
            }

            function addResult(testName, message, status, explanationKey) {
                results.push({
                    technical: `${results.length + 1}. ${testName}: ${message}`,
                    userFriendly: status === 'OK' ? translations[currentLang].success : translations[currentLang].error,
                    explanation: translations[currentLang].explanations[explanationKey]
                });
                resultDiv.innerHTML = results.map(result => `
                    <p>
                        <span style="color: #ffeb3b; font-weight: bold;">${result.technical}</span><br>
                        <span style="color: #4caf50; font-style: italic;">${result.userFriendly}</span><br>
                        <span class="explanation">${result.explanation}</span>
                    </p>
                `).join('');
                updateProgress();
            }

            // Тест 1: Проверка доступности сайта
            try {
                const response = await fetch(url, { mode: 'no-cors' });
                addResult(translations[currentLang].tests.accessibility, translations[currentLang].messages.accessible, 'OK', 'accessibility');
            } catch (error) {
                addResult(translations[currentLang].tests.accessibility, translations[currentLang].messages.notAccessible, 'Ошибка', 'accessibility');
            }

            // Тест 2: Проверка HTTPS
            const protocol = url.startsWith('https://') ? 'HTTPS' : 'HTTP';
            addResult(translations[currentLang].tests.https, protocol === 'HTTPS' ? translations[currentLang].messages.httpsUsed : translations[currentLang].messages.httpUsed, protocol === 'HTTPS' ? 'OK' : 'Ошибка', 'https');

            // Тест 3: Время загрузки
            try {
                const startTime = performance.now();
                await fetch(url, { mode: 'no-cors' });
                const loadTime = (performance.now() - startTime) / 1000;
                addResult(translations[currentLang].tests.loadTime, translations[currentLang].messages.loadTime(loadTime.toFixed(2)), loadTime < 2 ? 'OK' : 'Ошибка', 'loadTime');
            } catch (error) {
                addResult(translations[currentLang].tests.loadTime, translations[currentLang].messages.loadTimeFailed, 'Ошибка', 'loadTime');
            }

            // Тест 4: Корректность URL
            const urlPattern = /^(https?:\/\/)?([\w\d-]+\.)+[\w\d]{2,}(\/.*)?$/;
            addResult(translations[currentLang].tests.urlFormat, urlPattern.test(url) ? translations[currentLang].messages.urlValid : translations[currentLang].messages.urlInvalid, urlPattern.test(url) ? 'OK' : 'Ошибка', 'urlFormat');

            // Тест 5: Длина URL
            const urlLength = url.length;
            addResult(translations[currentLang].tests.urlLength, translations[currentLang].messages.urlLength(urlLength), urlLength <= 200 ? 'OK' : 'Ошибка', 'urlLength');

            // Тест 6: Проверка редиректов
            try {
                const response = await fetch(url, { redirect: 'manual', mode: 'no-cors' });
                if (response.status >= 300 && response.status < 400) {
                    addResult(translations[currentLang].tests.redirects, translations[currentLang].messages.hasRedirects(response.status), 'Ошибка', 'redirects');
                } else {
                    addResult(translations[currentLang].tests.redirects, translations[currentLang].messages.noRedirects, 'OK', 'redirects');
                }
            } catch (error) {
                addResult(translations[currentLang].tests.redirects, translations[currentLang].messages.redirectsFailed, 'Ошибка', 'redirects');
            }

            // Тест 7: Наличие протокола в URL
            const hasProtocol = url.startsWith('http://') || url.startsWith('https://');
            addResult(translations[currentLang].tests.protocol, hasProtocol ? translations[currentLang].messages.protocolPresent : translations[currentLang].messages.protocolMissing, hasProtocol ? 'OK' : 'Ошибка', 'protocol');

            // Тест 8: Наличие домена верхнего уровня
            const tldPattern = /\.[a-z]{2,}$/i;
            const hasTld = tldPattern.test(parsedUrl.hostname);
            addResult(translations[currentLang].tests.topLevelDomain, hasTld ? translations[currentLang].messages.tldValid : translations[currentLang].messages.tldInvalid, hasTld ? 'OK' : 'Ошибка', 'topLevelDomain');

            // Тест 9: Наличие поддоменов
            const subdomains = parsedUrl.hostname.split('.').length > 2;
            addResult(translations[currentLang].tests.subdomains, subdomains ? translations[currentLang].messages.subdomainsPresent : translations[currentLang].messages.subdomainsMissing, subdomains ? 'OK' : 'Ошибка', 'subdomains');

            // Тест 10: Использование кириллицы в URL
            const hasCyrillic = /[а-яА-Я]/.test(url);
            addResult(translations[currentLang].tests.cyrillic, hasCyrillic ? translations[currentLang].messages.cyrillicPresent : translations[currentLang].messages.cyrillicMissing, hasCyrillic ? 'Ошибка' : 'OK', 'cyrillic');

            // Тест 11: Наличие параметров в URL
            const hasParams = parsedUrl.search !== '';
            addResult(translations[currentLang].tests.parameters, hasParams ? translations[currentLang].messages.paramsPresent : translations[currentLang].messages.paramsMissing, hasParams ? 'OK' : 'Ошибка', 'parameters');

            // Тест 12: Количество слешей в пути
            const slashesCount = (parsedUrl.pathname.match(/\//g) || []).length;
            addResult(translations[currentLang].tests.slashes, translations[currentLang].messages.slashes(slashesCount), slashesCount <= 5 ? 'OK' : 'Ошибка', 'slashes');

            // Тест 13: Использование заглавных букв в URL
            const hasUppercase = /[A-Z]/.test(url);
            addResult(translations[currentLang].tests.uppercase, hasUppercase ? translations[currentLang].messages.uppercasePresent : translations[currentLang].messages.uppercaseMissing, hasUppercase ? 'Ошибка' : 'OK', 'uppercase');

            // Тест 14: Наличие ключевых слов в URL
            const keywords = ['blog', 'shop', 'news', 'about', 'contact'];
            const hasKeywords = keywords.some(keyword => url.toLowerCase().includes(keyword));
            addResult(translations[currentLang].tests.keywords, hasKeywords ? translations[currentLang].messages.keywordsPresent : translations[currentLang].messages.keywordsMissing, hasKeywords ? 'OK' : 'Ошибка', 'keywords');

            // Тест 15: Поддержка IPv4/IPv6 в домене
            const ipPattern = /^(?:[0-9]{1,3}\.){3}[0-9]{1,3}$|^(?:[0-9a-fA-F]{1,4}:){7}[0-9a-fA-F]{1,4}$/;
            const isIp = ipPattern.test(parsedUrl.hostname);
            addResult(translations[currentLang].tests.ipAddress, isIp ? translations[currentLang].messages.ipPresent : translations[currentLang].messages.ipMissing, isIp ? 'Ошибка' : 'OK', 'ipAddress');

            // Тест 16: Наличие порта в URL
            const hasPort = parsedUrl.port !== '';
            addResult(translations[currentLang].tests.port, hasPort ? translations[currentLang].messages.portPresent : translations[currentLang].messages.portMissing, hasPort ? 'Ошибка' : 'OK', 'port');

            // Тест 17: Кодирование символов в URL
            const hasEncoding = /%[0-9a-fA-F]{2}/.test(url);
            addResult(translations[currentLang].tests.encoding, hasEncoding ? translations[currentLang].messages.encodingPresent : translations[currentLang].messages.encodingMissing, hasEncoding ? 'OK' : 'Ошибка', 'encoding');

            // Тест 18: Частота использования дефисов
            const hyphenCount = (url.match(/-/g) || []).length;
            addResult(translations[currentLang].tests.hyphens, translations[currentLang].messages.hyphens(hyphenCount), hyphenCount <= 3 ? 'OK' : 'Ошибка', 'hyphens');

            // Тест 19: Наличие якорных ссылок
            const hasAnchor = parsedUrl.hash !== '';
            addResult(translations[currentLang].tests.anchor, hasAnchor ? translations[currentLang].messages.anchorPresent : translations[currentLang].messages.anchorMissing, hasAnchor ? 'OK' : 'Ошибка', 'anchor');

            // Тест 20: Проверка на подозрительные символы
            const suspiciousPattern = /[<>;(){}'"]/;
            const hasSuspicious = suspiciousPattern.test(url);
            addResult(translations[currentLang].tests.suspicious, hasSuspicious ? translations[currentLang].messages.suspiciousPresent : translations[currentLang].messages.suspiciousMissing, hasSuspicious ? 'Ошибка' : 'OK', 'suspicious');

            // Тест 21: Соответствие стандартам W3C для URL
            const w3cPattern = /^[a-zA-Z0-9\-._~:/?#[\]@!$&'()*+,;=]+$/;
            const isW3cCompliant = w3cPattern.test(url);
            addResult(translations[currentLang].tests.w3c, isW3cCompliant ? translations[currentLang].messages.w3cCompliant : translations[currentLang].messages.w3cNonCompliant, isW3cCompliant ? 'OK' : 'Ошибка', 'w3c');

            // Тест 22: Проверка статуса HTTP-запроса
            try {
                const response = await fetch(url, { method: 'HEAD' });
                addResult(translations[currentLang].tests.httpStatus, translations[currentLang].messages.httpStatus(response.status), response.status === 200 ? 'OK' : 'Ошибка', 'httpStatus');
            } catch (error) {
                addResult(translations[currentLang].tests.httpStatus, translations[currentLang].messages.httpStatusFailed, 'Ошибка', 'httpStatus');
            }

            // Тест 23: Проверка мета-тегов
            try {
                const response = await fetch(url);
                const text = await response.text();
                const parser = new DOMParser();
                const doc = parser.parseFromString(text, 'text/html');
                const title = doc.querySelector('title')?.textContent;
                const description = doc.querySelector('meta[name="description"]')?.content;
                const hasMetaTags = title && description && title.length <= 60 && description.length <= 160;
                addResult(translations[currentLang].tests.metaTags, hasMetaTags ? translations[currentLang].messages.metaTagsPresent : translations[currentLang].messages.metaTagsMissing, hasMetaTags ? 'OK' : 'Ошибка', 'metaTags');
            } catch (error) {
                addResult(translations[currentLang].tests.metaTags, translations[currentLang].messages.metaTagsMissing, 'Ошибка', 'metaTags');
            }

            // Тест 24: Проверка IoT-совместимости
            try {
                const response = await fetch(url);
                const headers = response.headers;
                const contentType = headers.get('content-type');
                const hasApi = contentType?.includes('json') || url.includes('/api/') || (await fetch(url + '/api', { method: 'OPTIONS' }).then(res => res.ok).catch(() => false));
                addResult(translations[currentLang].tests.iotCompatibility, hasApi ? translations[currentLang].messages.iotSupported : translations[currentLang].messages.iotNotSupported, hasApi ? 'OK' : 'Ошибка', 'iotCompatibility');
            } catch (error) {
                addResult(translations[currentLang].tests.iotCompatibility, translations[currentLang].messages.iotNotSupported, 'Ошибка', 'iotCompatibility');
            }
        }
    </script>
</body>
</html>
