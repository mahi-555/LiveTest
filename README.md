MONIRA MAHIN – MATH WHIZ

A web-based mathematical game built with **Java Servlets + JSP, JDBC and MySQL** on **Apache Tomcat 9**.
Players answer four questions (prime number, modulus, two trigonometry questions), see their score
out of 10, and appear on a **Top 10 leaderboard**.

> Every answer is calculated by Java methods at run time (`MathService`) – nothing is hard-coded.

## 1. Project Objectives

- Build reusable Java methods for prime checking, modulus and trigonometry.
- Apply conditional logic to score a four-question game (maximum 10 marks).
- Process requests and responses with a Java Servlet (MVC style with JSP views).
- Connect to MySQL with JDBC and `PreparedStatement` (INSERT + SELECT).
- Sort and present the Top 10 scores; document and submit the project on GitHub.

## 2. Technologies Used

| Layer | Technology |
|---|---|
| Language | Java 17 |
| Web | Servlet 4.0, JSP 2.3, JSTL 1.2, HTML5, CSS3 |
| Server | Apache Tomcat 9.x |
| Database | MySQL 8 + JDBC (`mysql-connector-j`) |
| Build | Maven (WAR) |
| IDE | IntelliJ IDEA |
| Tests | JUnit 5 |

## 3. Software Requirements

- JDK 17 or newer
- IntelliJ IDEA (Community works with Maven + a Tomcat run configuration via the *Smart Tomcat* plugin; Ultimate has Tomcat support built in)
- Apache Tomcat **9.x** (not 10+, which uses `jakarta.*` instead of `javax.*`)
- MySQL Server 8+ (and MySQL Workbench or the `mysql` CLI)
- Git

## 4. Project Structure

```
MathWhiz/
├── pom.xml
├── database/math_whiz_db.sql
├── screenshots/                 (add your 4 screenshots here)
├── src/main/java/
│   ├── controller/GameServlet.java   request handling (/game, /leaderboard)
│   ├── model/GameScore.java          DB row
│   ├── model/GameRound.java          the 4 generated questions
│   ├── model/GameResult.java         evaluated answers + score
│   ├── service/MathService.java      isPrime, calculateMod, calculateSin/Cos/Tan
│   ├── service/GameService.java      question generation + scoring
│   ├── dao/DBUtil.java               JDBC connection
│   └── dao/ScoreDAO.java             INSERT / SELECT with PreparedStatement
├── src/main/resources/db.properties  database URL, user, password
├── src/main/webapp/
│   ├── index.jsp  game.jsp  result.jsp  leaderboard.jsp
│   ├── css/style.css
│   └── WEB-INF/web.xml (game title), jspf/header+footer
└── src/test/java/service/MathServiceTest.java
```

## 5. Database Configuration

1. Start MySQL.
2. Run `database/math_whiz_db.sql` (below).
3. Open `src/main/resources/db.properties` and set your MySQL user/password:

```properties
db.url=jdbc:mysql://localhost:3306/math_whiz_db?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
db.user=root
db.password=your_mysql_password
```

## 6. Database Creation SQL

```sql
CREATE DATABASE IF NOT EXISTS math_whiz_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE math_whiz_db;

CREATE TABLE IF NOT EXISTS game_scores (
    id          INT          NOT NULL AUTO_INCREMENT,
    player_name VARCHAR(100) NOT NULL,
    score       INT          NOT NULL,
    difficulty  VARCHAR(20)  NOT NULL DEFAULT 'NORMAL',
    played_at   TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    INDEX idx_leaderboard (score DESC, played_at ASC)
);
```

Leaderboard query: `SELECT * FROM game_scores ORDER BY score DESC, played_at ASC LIMIT 10;`

## 7. Steps to Run the Application (IntelliJ IDEA)

1. **File → Open…** and select the `MathWhiz` folder (or its `pom.xml`) → *Open as Project*. Wait for Maven to download dependencies.
2. Create the database (section 6) and edit `db.properties` (section 5).
3. **Run → Edit Configurations → + → Tomcat Server → Local** (Ultimate) *or* install the **Smart Tomcat** plugin (Community).
   - Application server: your Tomcat 9 folder.
   - **Deployment** tab → **+ → Artifact → `MathWhiz:war exploded`**.
   - Application context: `/MathWhiz`.
4. Click **Run**. Open <http://localhost:8080/MathWhiz/>.
5. Optional – build a WAR: `mvn clean package`, then copy `target/MathWhiz.war` into Tomcat's `webapps/` folder.
6. Run unit tests: `mvn test`.

To change the title on every page, edit `appTitle` in `src/main/webapp/WEB-INF/web.xml`.

## 8. Game Rules and Scoring System

| Question | Area | Marks |
|---|---|---|
| Q1 | Prime number (answer **1** = prime, **0** = not prime) | 2 |
| Q2 | Modulus (integer operands only, divisor never 0) | 2 |
| Q3 | Trigonometry – sin / cos / tan of an angle in degrees | 3 |
| Q4 | Trigonometry – sin / cos / tan of an angle in degrees | 3 |
| | **Total** | **10** |

- Numbers less than 2 are not prime.
- `calculateMod` rejects a zero divisor with a validation error.
- Angles are in degrees and converted to radians before using `Math.sin/cos/tan`.
- `calculateTan` returns `NaN` where tangent is undefined (90°, 270°…); the game never asks those angles.
- Decimal answers are accepted within a tolerance of ±0.01 (round to 2 decimal places).
- Difficulty is **NORMAL** for all questions and is stored in the database with every score.

### Test cases (see `MathServiceTest`)

| Test | Expected |
|---|---|
| Prime 7 / 8 / 1 | 1 / 0 / 0 |
| 17 % 5 | 2 |
| sin 30° / cos 60° / tan 45° | 0.5 / 0.5 / 1.0 |
| Divisor 0 | Validation error |
| All answers correct | 10 marks |
| Incorrect answers | those marks not awarded |

## 9. Application Screenshots

### Welcome Page
![Welcome Page](screenshots/home.png)

### Game Page
![Game Page](screenshots/game.png)

### Result Page
![Result Page](screenshots/result.png)

### Leaderboard
![Leaderboard](screenshots/leaderboard.png)


 
