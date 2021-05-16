what is the objective?
target : two objects lead to the same fragment for one of them we send some data and for the other one we simply wont and thats how we determine which fragment should open 

how should we do it?
⦁	we added the addEditfragment.kt which is the fragment that opens wheter with click on an item of the list (edit) or the floating button (open)
⦁	we add the fragmet as a new destination to our nav graph, now we have an action in navigation component which is a navigation path as i said the way we distinguish between an add or edit situation is the argument that we send either we send a Word which means we want to edit it or we dont send anything that means we want a new one.
⦁	we add a new nullable argumant called Word of type costum parcelable which is null by default.
⦁	how to put the informatin in the corresponding views?
⦁	now we should put the sent values in the corresponding views of edit fragment.
⦁	  there are two places where we can get a handle to the argumant,we can get it either in the fragment or in the viewmodel but as we know the fragment should not be responsible for these kinds of tasks so we are going to do this in the viewmodel of fragment which holdes data for the fragment.
⦁	now we create the view model
⦁	it extends the viewmodel
⦁	alright we can retrieve the navigation argumrnt in the fragment and send it to the viewmodel and we also can retrieve it directly in the view model through the saved state handle 
⦁	 what is saved state handle
⦁	 we know we should save the data into our viewmodel because it survives the configuration changes when we rotate the device and the fragment gets destroyed and recreated but there is actually another type of destraction and recreation called proccess death .

⦁	imagine we type some inputs in the application (view of our fragment ) and we get a phone call our app goes to background and the system can decide to kill whole app porccess. because lack of the memory and in this case all of the data stored in our application will be lost.
⦁	 so we should take care of the porccess death.
⦁	saved instance state stores the little peaces of information which survive proccess death.
⦁	and the next time that we instanciate our fragment we can read this values and then recreate our ui properly.
how did we distinguish that its the first time that we are creating the app our its the second time and we have data in the saved instance state 6:48  +everything is nullable +we navigate to the correct page with the currect value which will be handled in this view model.

⦁	its the only way to store data between proccess death 
⦁	now we can read the value of saved instance state in the fragment and then pass it to the viewmodel or we can get a handle to it directly in the view model using a class called saved state handle
⦁	we add it to the constructor because dagger(hilt) can inject it automatically with @Assisted annotation some arguments need this annotation and saved state handle is one of them.
⦁	as we know saved state handle contains the little peaces of information that survive the configuration changes and will be added to ui later but it also contains the navigation arguments passed to the fragment as we inject the viewmodel to the fragment it can acceess those arguments and its just for convinient.
⦁	 val word = state.get<Word>("word") 
⦁	it gives you the word sent by the navigation fragment the name should be as the name of argument declered in the graph attributes and if it was null which means we didn't navigate to edit screen and its add new word the value will be null and the fragment would never know which page its presenting it just takes the value and show.
  var wordImportance = state.get<Boolean>("wordImportance") ?: word?.important ?: false
    set(value) {
        field = value
        state.set("wordImportance", value)
    }
⦁	alright here we are trying to put the value of "wordImportance" in the variable and we say whenever it changed get it from state.get() and put it in the state.set() so we wont lose it if proccess death happens again but the problem is if its not already in the state then where we are getting this from? and where are we actually setting the value of textViews in the instance state with the names that we gave them when actually the value comes from if its automatic how it actually distengush which instance state (name) we want?
⦁	13:16 we are using saved state handle here as well to store the value of search query.

⦁	AndoidEntyPoint enjects our ViewModel
⦁	we didnt distinguish edit and add fragment we are saying either ways use the variables in the viewmodel which its value can be either one of these options a) the value from state handle b) or the value from the word passed by the navigation arg c) null if the case is a it means its the second time that we are creating the view so we get the previous data, if its b we will have a value that comes from our navigation arg so in this case we are in the edit screen. C we dont have anything and its for adding //i guess so
⦁	now we should handle the navigation from the floating action button in the word fragment to the edit\add fragment.
⦁	we call a method in our viewmodel instead of handling the navigation in the fragment but the fragment should not be responsible for deciding what happens when we click the button beacause its not a good seperation of concerns and it doesnt let us unit test the viewmodel.
      here in the viewmodel we are deciding what page we should navigate to we do these by using events. in our sealed class we make an object called  NavigateToAddWordScreen because we dont want to pass any data so the sub class can be an object and it makes the code more efficient because it makes only one instance of the class. we also have a data class called NavigateToEditWordScreen which takes a word as the constructor.but actually channel and one time event are for doing something just one time for example showing a snackbar if we use a flow for the snackbar everytime that configuration change it will be shown again so we use these events in order to do everything once but here we also used them to communicate data between viewmodel and fragment and also we used it to observe and listen for events when we are sending (NavigateToAddWordScreen) event in the channel fragment which observe this will know it should navigate to add word screen here we actually dont send any data but its an event that fragment observe it.

⦁	and now we send an event to the channel in the function that will be called whenever floating button be clicked which means we are about to add a new word.and fragment listen to events in order to do appropriate action and its how we basicly tells the ui layer what to do.
⦁	we do the same thing with the on word selected function that gives you a word as the constructor that will be sent to you whenever an item from the list be selected. fragment listens to this event and gets the word that we are sending back to navigate to the word edit fragment so the data would be placed in the fragments.
⦁	now we should handle these new events that we sat up, in the fragment.
⦁	difference between statement and experession in kotlin for compile time safety get()=this extension property kotlin
⦁	action in navigation instead of R.id... gives us compile time safety which means if nav needs any argument and we dont pass them to it we get an error
⦁	


