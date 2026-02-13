
# 📜 MMF Logs - מערכת לוגים מרכזית

## 📚 חלק 1: כתיבת לוגים (Client Library)

הספרייה מאפשרת תיעוד היררכי (פרויקט > מודול), טיפול בשגיאות, ותיעוד ייעודי לקריאות API.

### ⚙️ הגדרות דוקר (Docker Compose)

כדי שהספרייה תהיה זמינה בקוד של המיקרו-שירות שלך, יש למפות את ה-Volume המתאים ולחבר לרשת הלוגים בקובץ `docker-compose.yml`:

```yaml
services:
  my-app:
    volumes:
      - ${LOGS} # טעינת ספריית הלוגים (מוגדר כמשתנה סביבה)
    networks: 
      - logs

# הגדרת הווליום והרשת כחיצוניים (נוצרו בשרת הלוגים)
volumes:
  logs:
    external: true

networks:
  logs:
    external: true

```

### 🚀 שימוש בקוד (Node.js)

#### 1. אתחול (Initialization)

יש לייבא את הספרייה וליצור מופע חדש עם שם הפרויקט ושם המודול הספציפי.

```javascript
const Logs = require('logs');

// מבנה: new Logs(ProjectName, ModuleName)
const log = new Logs('SmartRecords', 'CallHandler');

```

#### 2. רמות לוג (Log Levels)

**מידע כללי (Info):**

```javascript
log.info('התחלת שיחה חדשה', { 
    phone: '0501234567', 
    type: 'incoming' 
});

```

**דיבוג (Debug):**
משמש למידע טכני מפורט שלא צריך להופיע בלוגים הרגילים.

```javascript
log.debug('Parsing XML response', { rawXml: '<root>...</root>' });

```

**שגיאות (Error):**
ניתן להעביר אובייקט `Error` ישירות. המערכת תשמור את הודעת השגיאה ואת ה-Stack Trace המלא.

```javascript
try {
    // code...
} catch (err) {
    log.error('DB Connection Failed', err);
}

```

#### 3. הקשר שיחה (Call ID) - 💡 מומלץ!

כדי לקשר לוגים לשיחה ספציפית (לצורך חיפוש עתידי של כל אירועי השיחה), יש להעביר את הפרמטר `callId` בתוך אובייקט הנתונים.

```javascript
log.info('לקוח בחר שלוחה', { 
    callId: '170685555.123', 
    selection: 'support' 
});

```

#### 4. תיעוד אינטגרציות (API Requests)

פונקציה מיוחדת לתיעוד בקשות HTTP יוצאות ונכנסות. המידע נשמר במבנה ייעודי ב-DB המקל על דיבוג תקשורת.

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

---
