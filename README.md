from googleapiclient.discovery import build
import matplotlib.pyplot as plt
import csv

def youtube_search(video_id):
    # Your API Key
    DEVELOPER_KEY = "AIzaSyASBZT7EY2Ooctl6JpYAzZqdV10XUho06c"

    youtube = build('youtube', 'v3', developerKey=DEVELOPER_KEY)

    videos = []
    like_counts = []  # To store like counts for histogram

    # Fetch video details and write to CSV
    csvFile = open('video_result.csv', 'w')
    csvWriter = csv.writer(csvFile)
    csvWriter.writerow(["title", "videoId", "viewCount", "likeCount", "dislikeCount", "commentCount", "favoriteCount"])

    video_response = youtube.videos().list(id=video_id, part="statistics").execute()

    for video_result in video_response.get("items", []):
        title = video_result["snippet"]["title"]
        title = title.encode('utf-8').decode('unicode-escape')  # Handling Unicode characters
        videoId = video_result["id"]
        viewCount = video_result["statistics"]["viewCount"]
        likeCount = int(video_result["statistics"].get("likeCount", 0))
        dislikeCount = int(video_result["statistics"].get("dislikeCount", 0))
        commentCount = int(video_result["statistics"].get("commentCount", 0))
        favoriteCount = int(video_result["statistics"].get("favoriteCount", 0))

        csvWriter.writerow([title, videoId, viewCount, likeCount, dislikeCount, commentCount, favoriteCount])

        videos.append(title)
        like_counts.append(likeCount)

    csvFile.close()

    # Plotting histogram
    plt.hist(like_counts, bins=20, alpha=0.7, color='blue', edgecolor='black')
    plt.xlabel('Like Count')
    plt.ylabel('Frequency')
    plt.title('Histogram of Like Counts for YouTube Videos')
    plt.grid(True)
    plt.show()

if __name__ == "__main__":
    video_id = "fIlZtm5WpUHKUqUi"  # Replace this with your video ID
    youtube_search(video_id)

