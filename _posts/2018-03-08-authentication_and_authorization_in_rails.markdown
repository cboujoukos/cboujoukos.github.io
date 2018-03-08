---
layout: post
title:      "Authentication and Authorization in Rails"
date:       2018-03-08 21:17:23 +0000
permalink:  authentication_and_authorization_in_rails
---


These two, often confused, topics constitute a good portion of the security for your web app. And because any self-respecting app is both safe and secure, it is logical to have a strong understanding of how these concepts work.

## Authentication
Authentication is the process of identifying an individual, usually based on a username and password. This is process that occurs any time you log in to facebook, for example. This process is often best left to the professionals. Not only that, but there are already a couple of tried and true authentication schemes available for you, so you don't have to try to reinvent the wheel. I personally like working with the gem, Devise. Not only is it widely used, well-documented and well-maintained, but it also proides you with a ton of useful functionality right of of the box. Once you set up a user model with devise through the rails generator, you instantly have access to some very user helper methods such as `current_user`, `user_signed_in`, `user_session`, and the very helpful `before_action :authenticate_user!`. Running the generator also sets up all of your routes for you, as well as your standard registration and login forms. Devise also is easily used in combination with Omniauth to allow third-party authentication through trusted websites such as github or facebook (I will happily leave the responsibilty of authentication on the shoulders of these titans of tech). Now, that said, devise certainly has its drawbacks as well. It is cumbersome, customization is a nightmare, nested routing within a devise model has its flaws, among other things, but all in all, fthat price is not too steep for being able to rest easy knowing your app is secure.

Once you are logged in and your identity is confirmed, most of the other network security protocols full under the category of authorization.


## Authorization
Authorization is the function of specifying access rights and privileges to users in regards to various resources.  If a website requires authentication, it is pretty safe to say that they will also need some degree of authorization. There are a myriad of options out there to set up your authorization scheme. Devise Roles, Warden, Pundit, and CanCanCan are a few of the choices to pick from. For my rails project, though, I decided to roll out a simple role-based authorization scheme by adding a boolean type admin flag to my user model. The app essentially has three "roles", guest (someone that is not signed in), normal user, and admin. Using a simple boolean attribute for an admin role allowed me control who accessed what information with methods such as this one in my activities_controller:
```
def authorize
    unless current_user.try(:admin?) || current_user.id == Activity.find_by(id: params[:id]).user_id
      flash[:error] = "You are not authorized to view this page."
      redirect_back(fallback_location: root_path)
    end
  end
```
along with:
```
before_action :authorize, only: [:edit, :update, :destroy]
```

If my app had required any more robust authorization, such as additional roles, I would have certainly switched over to one of the aforementioned gems. 
