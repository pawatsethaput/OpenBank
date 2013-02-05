_I (Jan) propose the following:_
_Problem: the links would be helpful for a human using a browser to explore and test the API, however the links are not clickable and the data returned as JSON is not ideal for human consumption. The links returned in the JSON on the other side are not really helpful for API consuming applications, because they are typically not exploring the API._
_Solution: the HTTP Accept header would be the httpish way of differentiating between these two cases. Browser sent an Accept header of text/html. In this case an implementation should return the data rendered as html and the links as a-tags with title and href attributes. This way the API can quickly be explored and tested by a human. API consuming applications on the other send would send an Accept header of x-application/json in which case we'd return the json doc as is._