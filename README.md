from http.server import SimpleHTTPRequestHandler, HTTPServer
import urllib.parse
import random


def simulate_sentiment(brand):
    # Randomly simulate sentiment based on brand
    positive = random.randint(40, 70)
    neutral = random.randint(10, 30)
    negative = 100 - positive - neutral
    reddit_posts = random.randint(50, 120)
    x_posts = random.randint(40, 100)

    return positive, neutral, negative, reddit_posts, x_posts


class Handler(SimpleHTTPRequestHandler):

    def do_GET(self):
        params = urllib.parse.urlparse(self.path)
        query = urllib.parse.parse_qs(params.query)

        brand = query.get('brand', [''])[0]

        if brand:
            positive, neutral, negative, reddit_posts, x_posts = simulate_sentiment(
                brand)
            content = f"""
            <html>
            <head><title>Sentiment for {brand}</title></head>
            <body style="font-family:Arial; margin:40px; background:#f9f9f9;">
            <h1>Sentiment Analysis for "{brand}"</h1>

            <h2>Sentiment Summary</h2>
            <p>Positive: {positive}%</p>
            <p>Neutral: {neutral}%</p>
            <p>Negative: {negative}%</p>

            <h2>Platform Comparison</h2>
            <p>Reddit Posts: {reddit_posts}</p>
            <p>X Posts: {x_posts}</p>

            <h2>Key Insights</h2>
            <ul>
            <li>Overall sentiment is mostly {"positive" if positive > negative else "negative"}.</li>
            <li>Reddit users often discuss product experiences in detail.</li>
            <li>X users show more immediate reactions and engagement.</li>
            </ul>

            <a href="/">← Back to dashboard</a>

            </body>
            </html>
            """
        else:
            content = """
            <html><head><title>AI Sentiment Dashboard</title></head>
            <body style="font-family:Arial; margin:40px; background:#f9f9f9;">

            <h1>AI Social Media Sentiment Dashboard</h1>

            <form method="GET">
            <label>Enter a brand or keyword:</label><br><br>
            <input type="text" name="brand" placeholder="e.g., Nike" style="width:300px; padding:8px;">
            <button type="submit" style="padding:8px 12px;">Analyze</button>
            </form>

            <p>Try entering a brand name to see simulated sentiment results.</p>

            </body>
            </html>
            """

        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        self.wfile.write(content.encode())


server = HTTPServer(("0.0.0.0", 8000), Handler)

print("Server running")
server.serve_forever()
