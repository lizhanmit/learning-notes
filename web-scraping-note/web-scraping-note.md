# Web Scraping Note

[A Practical Introduction to Web Scraping in Python](https://realpython.com/python-web-scraping-practical-introduction/)

Beautiful Soup does not provide any way to work with HTML forms. For example, if you need to search a website for some query and then scrape the results, then Beautiful Soup alone will not get you very far. Use MechanicalSoup instead.

MechanicalSoup uses Beautiful Soup to parse the HTML from the request.

Most websites publish a "Terms of Use" document, which can often be found through a link in the footer of the website. Always read this document before attempting to scrape data from a website. If you cannot find the Terms of Use, then try to contact the website owner and ask them if they have any policies regarding request volume. Failure to comply with the Terms of Use could result in your IP being blocked.