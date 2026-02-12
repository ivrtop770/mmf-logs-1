
### 📝 כתיבת לוגים (Client Library Documentation)

הספרייה (`logs`) מאפשרת כתיבה אסינכרונית לשרת הלוגים המרכזי. הספרייה תומכת בתיעוד היררכי (פרויקט > מודול), טיפול בשגיאות, ותיעוד קריאות API.

## ⚙️ הגדרות דוקר (Docker Compose)

כדי שהספרייה תהיה זמינה בקוד, יש למפות אותה ב-`docker-compose.yml` לדוגמא:

```yaml
services:
  my-app:
    volumes:
      - ${LOGS} # טעינת הספרייה
    networks: 
      - logs


volumes:
  logs:
    external: true

networks:
  logs:
    external: true
```

## 🚀 שימוש בקוד (Node.js)

### 1. אתחול (Initialization)

יש לייבא את הספרייה וליצור מופע עם שם הפרויקט ושם המודול הספציפי. לדוגמא.

```javascript
const Logs = require('logs');

// new Logs(ProjectName, ModuleName)
const log = new Logs('SmartRecords', 'CallHandler');

```

### 2. רמות לוג (Log Levels)

**מידע כללי (Info):**

```javascript
log.info('התחלת שיחה חדשה', { 
    phone: '0501234567', 
    type: 'incoming' 
});

```

**דיבוג (Debug):**
משמש למידע טכני שלא צריך להופיע בלוגים הרגילים.

```javascript
log.debug('Parsing XML response', { rawXml: '<root>...</root>' });

```

**שגיאות (Error):**
ניתן להעביר אובייקט `Error` ישירות. המערכת תשמור את ה-Stack Trace המלא.

```javascript
try {
    // code...
} catch (err) {
    log.error('DB Connection Failed', err);
}

```

### 3. הקשר שיחה (Call ID) - 💡 מומלץ!

כדי לקשור לוגים לשיחה ספציפית (לצורך חיפוש עתידי), יש להעביר את הפרמטר `callId` בתוך האובייקט.

```javascript
log.info('לקוח בחר שלוחה', { 
    callId: '170685555.123', 
    selection: 'support' 
});

```

### 4. תיעוד אינטגרציות (API Requests)

פונקציה מיוחדת לתיעוד בקשות HTTP יוצאות ונכנסות. המידע נשמר במבנה ייעודי ב-DB.

**תחביר:**
`log.api(description, { callId, url, method, req, res })`

**דוגמה:**

```javascript
log.api('בדיקת מספר מול ימות המשיח', {
    callId: '170685555.123',
    url: 'https://api.exapmle.co.il/check',
    method: 'POST',
    req: { body: { phone: '050...' } }, // מה שלחנו
    res: { status: 200, data: { ... } } // מה קיבלנו
});

```
