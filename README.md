Youtube API Step wise guide: 

Follow below steps for using youtube API:

1. First we need to get the SHA-1 fingerprint on your machine using java keytool. Execute the below command in cmd/terminal to get the SHA-1 fingerprint.

keytool -list -v -keystore "%USERPROFILE%\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android

2. Go to Google Developer Console and select or create a new project.
3. On the left sidebar, select APIs under APIs & auth and turn the status ON for YouTube Data API v3.
4. On the left sidebar, select Credentials and Create new key under Public API acess.
5. When popup comes asking you to choose platform, select Android Key.
6. Paste the SHA-1 key and your project’s package name separated by semicolon(;).
7. Click on create. Now you should see the API KEY on the dashboard.

8. Download the latest of version of YouTube Android Player API and extract it. Once extracted, you can find YouTubeAndroidPlayerApi.jar file inside libs folder.
9. Paste the YouTubeAndroidPlayerApi.jar file in your project’s libs folder.


10. Create a class named Config.java to keep our app configuration variables like Google Developer Key and YouTube video id.
In the below class, you need to replace the DEVELOPER_KEY with your own API KEY that we generated in the Google Developer Console.

 Config.java
package info.androidhive.youtubeplayer;
    public class Config {
        // Google Console APIs developer key
        // Replace this key with your's
        public static final String DEVELOPER_KEY = "AIzaSyABYoczeHg4XABx_jMRfv-CqmA2YMsIY4A";
        // YouTube video id
        public static final String YOUTUBE_VIDEO_CODE = "_oEA18Y8gM0";
    }

11. In XML add youtube player
<com.google.android.youtube.player.YouTubePlayerView
                android:id="@+id/youtube_view"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="30dp" />






12. Add below code to MainActivity
MainActivity.java
package info.androidhive.youtubeplayer;
 
import android.content.Intent;
import android.os.Bundle;
import android.view.Window;
import android.view.WindowManager;
import android.widget.Toast;
 
import com.google.android.youtube.player.YouTubeBaseActivity;
import com.google.android.youtube.player.YouTubeInitializationResult;
import com.google.android.youtube.player.YouTubePlayer;
import com.google.android.youtube.player.YouTubePlayer.PlayerStyle;
import com.google.android.youtube.player.YouTubePlayerView;
    public class MainActivity extends YouTubeBaseActivity implements
            YouTubePlayer.OnInitializedListener {
        private static final int RECOVERY_DIALOG_REQUEST = 1;
        // YouTube player view
        private YouTubePlayerView youTubeView;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            requestWindowFeature(Window.FEATURE_NO_TITLE);
            getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
                    WindowManager.LayoutParams.FLAG_FULLSCREEN);
            setContentView(R.layout.activity_main);
            youTubeView = (YouTubePlayerView) findViewById(R.id.youtube_view);
            // Initializing video player with developer key
            youTubeView.initialize(Config.DEVELOPER_KEY, this);
        }
        @Override
        public void onInitializationFailure(YouTubePlayer.Provider provider,
                                            YouTubeInitializationResult errorReason) {
            if (errorReason.isUserRecoverableError()) {
                errorReason.getErrorDialog(this, RECOVERY_DIALOG_REQUEST).show();
            } else {
                String errorMessage = String.format(
                        getString(R.string.error_player), errorReason.toString());
                Toast.makeText(this, errorMessage, Toast.LENGTH_LONG).show();
            }
        }
        @Override
        public void onInitializationSuccess(YouTubePlayer.Provider provider,
                                            YouTubePlayer player, boolean wasRestored) {
            if (!wasRestored) {
                // loadVideo() will auto play video
                // Use cueVideo() method, if you don't want to play it automatically
                player.loadVideo(Config.YOUTUBE_VIDEO_CODE);
                // Hiding player controls
                player.setPlayerStyle(PlayerStyle.CHROMELESS);
            }
        }
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            if (requestCode == RECOVERY_DIALOG_REQUEST) {
                // Retry initialization if user performed a recovery action
                getYouTubePlayerProvider().initialize(Config.DEVELOPER_KEY, this);
            }
        }
        private YouTubePlayer.Provider getYouTubePlayerProvider() {
            return (YouTubePlayerView) findViewById(R.id.youtube_view);
        }
    }

