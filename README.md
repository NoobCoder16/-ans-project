# android studio project

# G UI - wireframe
Team Figma : https://www.figma.com/file/TAEtcljsu3w3VIKIkLjMtS/%EA%B0%9D%ED%94%84-%ED%8C%80?type=design&node-id=0%3A1&mode=design&t=VoHskRnxfD7MEwLA-1

< 화면 구성 >
    
    1. Home ( 최근순 여행 프로젝트 간략 View 정렬 )
    2. 여행 프로젝트 별 세부 정보 page
    3. 여행 프로젝트 별 세부 정보 수정 page
    4. 새 여행 프로젝트 입력 page
    5. 카테고리 수정 page
    6. 통계 page + 통계 내용 선택 page

< R&R >

    김정호 : 통계 page + 통계 내용 선택 page
    김형석 :여행 프로젝트 별 세부 정보 + 수정 page
    문정우 : Home & 새 여행 입력 page
    공통 : DB 구축 및 카테고리 수정 page

# application name pallet(pal+let's travel)

초기화면 구상 skeleton @week 2
import android.content.ContentValues
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper
import android.content.Context

class ExpenseDatabaseHelper(context: Context) : SQLiteOpenHelper(context, DATABASE_NAME, null, DATABASE_VERSION) {

    companion object {
        private const val DATABASE_VERSION = 1
        private const val DATABASE_NAME = "ExpenseTrackerDB.db"
        private const val TABLE_EXPENSES = "expenses"
        private const val COLUMN_ID = "id"
        private const val COLUMN_DATE = "date"
        private const val COLUMN_CATEGORY = "category"
        private const val COLUMN_AMOUNT = "amount"
        private const val COLUMN_DESCRIPTION = "description"
    }

    override fun onCreate(db: SQLiteDatabase) {
        val createTableQuery = "CREATE TABLE $TABLE_EXPENSES (" +
                "$COLUMN_ID INTEGER PRIMARY KEY AUTOINCREMENT," +
                "$COLUMN_DATE TEXT," +
                "$COLUMN_CATEGORY TEXT," +
                "$COLUMN_AMOUNT REAL," +
                "$COLUMN_DESCRIPTION TEXT" +
                ")"
        db.execSQL(createTableQuery)
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        db.execSQL("DROP TABLE IF EXISTS $TABLE_EXPENSES")
        onCreate(db)
    }

    fun addExpense(expense: Expense) {
        val values = ContentValues()
        values.put(COLUMN_DATE, expense.date)
        values.put(COLUMN_CATEGORY, expense.category)
        values.put(COLUMN_AMOUNT, expense.amount)
        values.put(COLUMN_DESCRIPTION, expense.description)

        val db = this.writableDatabase
        db.insert(TABLE_EXPENSES, null, values)
        db.close()
    }
}

data class Expense(val date: String, val category: String, val amount: Double, val description: String)
//sqlite를 사용하여 데이터 저장
# 참조 https://developer.android.com/training/data-storage/sqlite?hl=ko
class HomeActivity : AppCompatActivity() {
    private lateinit var dbHelper: ExpenseDatabaseHelper

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_home)

        dbHelper = ExpenseDatabaseHelper(this)

        val saveButton = findViewById<Button>(R.id.saveButton)
        saveButton.setOnClickListener {
            val date = dateEditText.text.toString()
            val category = categoryEditText.text.toString()
            val amount = amountEditText.text.toString().toDouble()
            val description = descriptionEditText.text.toString()

            val expense = Expense(date, category, amount, description)
            dbHelper.addExpense(expense)

            // You can also clear the input fields after saving the expense.
            dateEditText.text.clear()
            categoryEditText.text.clear()
            amountEditText.text.clear()
            descriptionEditText.text.clear()
        }
    }
}
