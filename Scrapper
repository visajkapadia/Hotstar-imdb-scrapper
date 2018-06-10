import requests
from selenium import webdriver
from bs4 import BeautifulSoup


class Scrapper:

    __link = None
    __keyword = None
    __soup = None
    __rating = None
    __total_votes = None
    __is_available_on_prime_videos = False
    __main_cast = None
    __prime_video_link = None
    __directors = []
    __actors = []
    __writer = None
    __movie_name = None
    __movie_year = None
    __running_time = None
    __summary = None
    __trailer_link = None
    __poster = None
    __category = None
    __hotstar_movie_list = []
    __parents_guide = None  # key : list

    def __init__(self, keyword):
        self.__keyword = keyword.strip().replace(" ", "+") + "+imdb"
        temp_soup = BeautifulSoup(requests.get('https://www.google.com/search?q={'+self.__keyword+'}&num={}&hl={\'en\'}'
                                               ).content, "html.parser")
        div = temp_soup.find("div", {'class': 'g'})
        self.__link = div.find('h3', {'class': 'r'}).find('a')['href']
        last_index = self.__link.rfind('/')
        first_index = 7
        imdb_link = self.__link[first_index: last_index+1]
        self.__soup = BeautifulSoup(requests.get(imdb_link).content, "html.parser")

    def get_movie_name(self):
        if self.__soup is not None:
            self.__movie_name = self.__soup.find("meta", property="og:title")['content']
            last_index = self.__movie_name.rfind('(')
            self.__movie_name = self.__movie_name[: last_index].strip()
            return self.__movie_name

    def get_rating(self):  # <span itemprop="ratingValue">6.8</span>
        if self.__soup is not None:
            return self.__soup.find("span", itemprop="ratingValue").text

    def get_total_votes(self):  # <span class="small" itemprop="ratingCount">33,935</span>
        if self.__soup is not None:
            return self.__soup.find("span", itemprop="ratingCount").text

    def is_available_on_prime_videos(self):
        if self.__soup is not None:
            if self.__soup.find("div", {'class': 'watch-icon winner provider prime-instant-video'}):
                return True
            return False

    def get_main_cast(self):
        if self.__soup is not None:
            span_tags = self.__soup.find_all("span", itemprop="actors")
            for span in span_tags:
                self.__actors.append(span.find("span").text)
        return self.__actors

    def get_prime_video_link(self):
        pass

    def get_hotstar_details(self):
        movie_name = self.__movie_name.strip().replace(" ", "%20")
        browser = webdriver.Chrome(executable_path=r'/Users/visajkapadia/PycharmProjects/PyTest/venv/selenium'
                                                   r'/webdriver/chrome/chromedriver')
        browser.get("http://www.hotstar.com/search?q=" + movie_name)
        html = browser.page_source
        temp_soup = BeautifulSoup(html, "html.parser")
        articles = temp_soup.find_all('article')
        for article_tag in articles:
            link = article_tag.find('a')['href']
            if link.startswith('/movies/'):
                temp_list = []
                name = article_tag.find('h3', {'class': 'ng-binding title'}).text
                temp_list.append(name)
                thumbnail = article_tag.find('img')['src']
                premium = article_tag.find('div', {'class': 'overlay premium ng-scope'})
                if premium:
                    temp_list.append(True)
                else:
                    temp_list.append(False)
                temp_list.append(thumbnail)
                subdetails = article_tag.find('span', {'class': 'subdetails ng-binding'}).text
                temp_list.append(subdetails)
                temp_list.append('http://www.hotstar.com' + link)
                self.__hotstar_movie_list.append(temp_list)
        return self.__hotstar_movie_list

    def get_director(self):
        if self.__soup is not None:
            span_tags = self.__soup.find_all("span", itemprop="director")
            for span in span_tags:
                self.__directors.append(span.find("span").text)
            return self.__directors

    def get_movie_year(self):  # <span id="titleYear"> ( <a href="/year/2017/?ref_=tt_ov_inf"> 2017 </a>) </span>
        if self.__soup is not None:
            span = self.__soup.find("span", id="titleYear")
            return span.find("a").text

    def get_running_time(self):
        if self.__soup is not None:
            return self.__soup.find('time', itemprop="duration").text.strip()

    def get_summary(self):  # <div class="summary_text">
        if self.__soup is not None:
            return self.__soup.find("div", {"class": "summary_text"}).text.strip()

    def get_trailer_link(self):
        pass

    def get_poster(self):
        if self.__soup is not None:
            return self.__soup.find("div", {"class": "poster"}).find("img")['src']

    def get_category(self):
        if self.__soup is not None:
            category = []
            anchor_tags = self.__soup.find('div', itemprop="genre").find_all('a')
            for a in anchor_tags:
                category.append(a.text)
            return category

    def get_youtube_search(self):
        pass

    def get_parents_guide(self):
        pass

movie_name = "doctor strange"
data = Scrapper(movie_name)

print("POSTER: " + data.get_poster())
print(data.get_movie_name() + " (" + data.get_movie_year() + ")")
print(data.get_summary()) # sometimes also gives "see full summary >>" in summary
print("IMDB: " + data.get_rating() + "/10")
print("DURATION: ", data.get_running_time())
print(data.get_category())
print(data.get_main_cast())
print("DIRECTOR: " + data.get_director()[0])
print("ON PRIME VIDEOS: " + str(data.is_available_on_prime_videos()))
print("HOTSTAR: " + str(data.get_hotstar_details()))  # [name, premium, thumbnail, subdetails(movie type, language, year), link]

