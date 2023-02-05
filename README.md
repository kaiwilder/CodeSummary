# Live Project
## Introduction
For 2 weeks of my time at The Tech Academy I spent working on a .NET Framework MVC Website project for a local Theater company with my fellow students. It makes use of Entity framework to connect to and update the database. The Live Project mimics an internship that uses Agile and Scrum methodologies, where we did 2 week sprints with daily stand up meetings and a code retrospective each friday.

## Back End Stories

I was tasked with creating a comments page with CRUD functionality that represent comments on a blog post. The model contains a constructor method that prints the date and time a comment was created. It has a Like Ratio method that is applied in the partial view, and is dynamically updated with ajax to update the progress bar below a comment.

### Comment Model
```
public class Comment
    {
        public int CommentId { get; set; }
        public string Message { get; set; }
        public DateTime CommentDate { get; set; }
        public int Likes { get; set; }
        public int Dislikes { get; set; }
        public ApplicationUser Author { get; set; }


        public Comment()
        {
            CommentDate = DateTime.Now;
        }

        public double LikeRatio()
        {
            double percentage = Likes / (Likes + Dislikes);
            return percentage;
        }

    }
```

Here I used Razor syntax in my onclick event listener to connect the like and dislike buttons with the respective likes and dislikes columns in the comments table in the database. I also altered the Delete ActionLink to be a button as well, and added in a progress bar that reflects the ratio of likes to dislikes.

### Comments Partial View
```
<div>
    @foreach (var item in Model)
    {
    <div class="card comment-index--container">
        <div class="card-body">
            <h5 class="card-text"> @Html.DisplayFor(modelItem => item.Author)</h5>
            <h6 class="card-subtitle mb-2 text-muted">@Html.DisplayFor(modelItem => item.CommentDate)</h6>
            <h4 class="card-title">@Html.DisplayFor(modelItem => item.Message)</h4>
          
            <button type="submit" class="btn btn-primary btn-sm"  id="like" onclick="Likes(@item.CommentId)">Like</button>
      
            <button type="submit" class="btn btn-primary btn-sm" id="dislike" onclick="Dislikes(@item.CommentId)">Dislike</button>

            <button type="submit" class="btn btn-primary btn-sm">Reply</button>
            @Html.ActionLink("Delete", "Delete", new { id = item.CommentId }, new { @class = "btn btn-primary btn-sm comment-index-btn" })
           

            <div>@Html.ActionLink("Edit", "Edit", new { id = item.CommentId }) </div>
            <div>@Html.ActionLink("Details", "Details", new { id = item.CommentId })  </div>

        </div>
    </div>
    }
</div>

```

This function in the CommentsController finds the id of each individual comment and increases the number of likes or dislikes in the database with each button click.

### Incriment likes and dislikes functionality
```
[HttpPost]
  public JsonResult AddLikes(int id)
  {
      var comment = db.Comments.Find(id);
      comment.Likes += 1;
      db.Entry(comment).State = EntityState.Modified;
      db.SaveChanges();
      var result = new JsonResult();
      return Json(result);
  }

[HttpPost]
  public JsonResult AddDislikes(int id)
  {
      var comment = db.Comments.Find(id);
      comment.Dislikes += 1;
      db.Entry(comment).State = EntityState.Modified;
      db.SaveChanges();
      var result = new JsonResult();
      return Json(result);
  }
```        

I've used ajax and jquery to asynchronously update the number of likes and dislikes displayed next to their respective buttons, as well as the progress bar below them.

### Async updates with ajax
```
function Likes(id) {
    $.ajax({
        type: "POST",
        url: "/Comments/AddLikes",
        data: {id: id},
    })
}

function Dislikes(id) {
    $.ajax({
        type: "POST",
        url: "/Comments/AddDislikes",
        data: {id: id},
    })
}

```

## Front End Stories

Along with creating a Comments page with functionality I was tasked with styling the comment format, and a developer sign in page.

### Comment page styling
```
.comment-index--container {
    width: 50%;
    background-color: var(--secondary-color);
    border-radius: 20px;
}
```

I've divided the names into 4 columns here, to eliminate empty page space. I've also made it so any overflow is hidden and will instead show elipses at the end, indicating the name is longer than can cleanly fit in the container. This also keeps the page looking cleaner on smaller screens.

### Sign in page styling
```
.home-signin--container {
    column-count: 4;    /*creates 4 columns of names on the page*/
}

    .home-signin--container > p { /*gives <p> specific stylings, any overflow is */
        background-color: var(--main-color);        /* hidden and ellipses are shown instead */
        text-align: center;
        padding-top: 5px;
        padding-bottom: 5px;
        border-radius: 20px;
        text-overflow: ellipsis;
        white-space: nowrap;
        overflow: hidden;
    }
```    
