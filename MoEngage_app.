import android.app.Activity
import android.content.Intent
import android.os.Bundle
import android.support.v7.app.AppCompatActivity
import android.view.View
import android.widget.ListView
import android.widget.TextView
import com.google.firebase.messaging.FirebaseMessaging
import com.google.firebase.messaging.RemoteMessage
import com.google.gson.Gson
import java.net.URL
import java.text.SimpleDateFormat
import java.util.*
import kotlin.collections.ArrayList

class MainActivity : AppCompatActivity() {

    private lateinit var listView: ListView
    private lateinit var firebaseMessaging: FirebaseMessaging
    private val newsArticles = ArrayList<NewsArticle>()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        listView = findViewById(R.id.listView)
        firebaseMessaging = FirebaseMessaging.getInstance()

        // Fetch news articles from the API and list them on the home screen
        fetchAndDisplayNewsArticles()

        // Implement Firebase Cloud Messaging to receive and show notifications
        setupFirebaseCloudMessaging()

        // Implement a feature to sort and list articles based on old-to-new and new-to-old
        implementNewsArticleSorting()

        // Implement custom design, font, and icons
        implementCustomDesign()

        // Add user-friendly features
        addUserFriendlyFeatures()

        // Handle input exceptions, Unicode, and null values
        handleExceptionsAndValues()

        // Beautify and comment the code
        beautifyAndCommentCode()
    }

    private fun fetchAndDisplayNewsArticles() {
        val url = URL("https://candidate-test-data-moengage.s3.amazonaws.com/Android/news-api-feed/staticResponse.json")
        val connection = url.openConnection()
        val inputStream = connection.getInputStream()
        val reader = BufferedReader(InputStreamReader(inputStream))
        val response = reader.readText()

        val gson = Gson()
        val newsArticleData = gson.fromJson(response, Array<NewsArticleData>::class.java)

        for (newsArticleDatum in newsArticleData) {
            try {
                val publishedAt = SimpleDateFormat("yyyy-MM-dd HH:mm:ss").parse(newsArticleDatum.publishedAt)
                val newsArticle = NewsArticle(newsArticleDatum.headline, newsArticleDatum.url, publishedAt)
                newsArticles.add(newsArticle)
            } catch (e: Exception) {
                Log.e("MainActivity", "Error parsing news article data: ${e.message}")
            }
        }

        val adapter = NewsArticleAdapter(this, newsArticles)
        listView.adapter = adapter
    }

    private fun setupFirebaseCloudMessaging() {
        firebaseMessaging.subscribeToTopic("news") { senderId, success ->
            if (success) {
                Log.d("MainActivity", "Subscribed to topic: news")
            } else {
                Log.d("MainActivity", "Failed to subscribe to topic: news")
            }
        }

        firebaseMessaging.isAutoInitEnabled = true
        firebaseMessaging.token.addOnCompleteListener { task ->
            if (task.isSuccessful) {
                val token = task.result
                Log.d("MainActivity", "Firebase Cloud Messaging token: $token")
            } else {
                Log.d("MainActivity", "Failed to get Firebase Cloud Messaging token")
            }
        }

        firebaseMessaging.addMessageHandler { remoteMessage ->
            // Handle data payload of FCM
            val data = remoteMessage.data

            try {
                val headline = data["headline"] ?: ""
                val url = data["url"] ?: ""

                val notificationIntent = Intent(this, NewsArticleActivity::class.java)
                notificationIntent.putExtra("headline", headline)
                notificationIntent.putExtra("url", url)

                startActivity(notificationIntent)
            } catch (e: Exception) {
                Log.e("MainActivity", "Error handling FCM data payload: ${e.message}")
            }
        }
    }

   private fun implementNewsArticleSorting() {
    val sortByOldToNew = findViewById<View>(R.id.sortByOldToNew)
    sortByOldToNew.setOnClickListener {
        newsArticles.sortBy { it.publishedAt }
        val adapter = NewsArticleAdapter(this, newsArticles)
        listView.adapter = adapter
    }

    val sortByNewToOld = findViewById<View>(R.id.sortByNewToOld)
    sortByNewToOld.setOnClickListener {
        newsArticles.sortByDescending { it.publishedAt }
        val adapter = NewsArticleAdapter(this, newsArticles)
        listView.adapter = adapter
    }
}
