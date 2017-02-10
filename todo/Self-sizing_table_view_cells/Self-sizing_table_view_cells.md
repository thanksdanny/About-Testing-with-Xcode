原文地址：https://www.raywenderlich.com/129059/self-sizing-table-view-cells

Note: This tutorial has been updated (21/09/2016) to use Xcode 8, iOS 10, and Swift 3. The original post was written by Joshua Greene.
If you’ve ever created custom table view cells before, chances are good that you have spent a lot of time sizing table view cells in code. You may even be familiar with having to manually calculate the height of every label, image view, text field, and everything else within the cell.
Frankly, this approach is mind-boggling and error prone.
In this self-sizing table view cells tutorial, you’ll learn how to create and dynamically size table view cells to fit their contents. You might be thinking, “That’s going to take a lot of work…!”
Nope. :] Lucky for you, Apple made this much easier in iOS 8!



Note: This tutorial requires Xcode 8 or newer in order to be compatible with Swift 3 syntax.
This tutorial also assumes you have a basic familiarity with Auto Layout, UITableView and Swift development. If you’re new to iOS or Swift development, you should check out some of the other written and video tutorials on this site.



Getting Started
Back in the days of iOS 6, Apple introduced a wonderful new technology: Auto Layout. Developers rejoiced; parties commenced in the streets; bands wrote songs to celebrate its greatness…
Okay, so that might be a stretch, but it was a big deal.
While it inspired hope for developers, Auto Layout was still cumbersome. Manually writing Auto Layout code was, and still is, a great example of the verbosity of iOS development. Interface Builder, as well, was initially quite inefficient when setting up constraints.
Flash forward to now. With all the improvements to Interface Builder and the introduction of iOS 8, it’s easy to use Auto Layout to create self-sizing table view cells!
A few ins-and-outs aside, all you really have to do is:
Use Auto Layout when creating your table view cells.
Set the table view rowHeight to UITableViewAutomaticDimension.
Set the estimatedRowHeight or implement the height estimation delegate method.
But, you don’t want to delve into theory right now, do you? You’re ready to get down to coding, so let’s get to business with the project.



Tutorial App Overview
Imagine that your top client has come to you and said, “I want an app that shows off some off the most famous artists that have ever lived, and some of their most prominent works!”
“We started making the app, but we’re stumped at how to display the content in a table view,” your client admits. “Can you make it work?”
You suddenly feel the urge to slide into the nearest phone booth and change into your cape.



But you don’t need gimmicks to be your client’s hero – your programming skills will suffice!
First, download the “client’s code” – Artistry-Starter – the starter project for this tutorial on self-sizing table view cells. Expand the zip file and open the project in Xcode.
Open Main.storyboard (in the Views group under the Artistry project.) You’ll see three scenes:



From left to right, they are:
A top-level navigation controller
ArtistListViewController shows a list of artists
ArtistDetailViewController displays an artist’s works and information about each one
Build and run. You’ll see a list of artists displayed in ArtistListViewController. Select the first artist (Pablo Picasso), and the app will segue to the ArtistDetailViewController, which displays a list of the selected artist’s works:




Not only is the app missing images of each artist and each work, but the information you are trying to display is getting cut off! Each piece of information and image will be a different size, so you can’t just increase the table view cell height and call it a day. Your cell heights will need to be dynamic, based on the content of each cell.
You’ll start by implementing dynamic cell heights in ArtistListViewController.



Self-Sizing Table View Cells
To get dynamic cell heights working properly, you need to create a custom table view cell and set it up with the right Auto Layout constraints.
In the project navigator select the Views group and press Command-N to create a new file in this group. Create a new Cocoa Touch Class called ArtistTableViewCell and make it a subclass of UITableViewCell.
Open ArtistTableViewCell.swift, delete the two automatically generated methods and add the following property:
@IBOutlet weak var bioLabel: UILabel!
Next, open Main.storyboard, select the cell in the table view of the ArtistListViewController. In Identity Inspector change Class to ArtistTableViewCell:
self-sizing table view cells
Drag and drop a new UILabel onto the cell, and set the text to Bio. Set the new label’s Lines property (the number of lines the label can have at most) to 0 in the Identity Inspector. It should look like this:



Setting the number of lines is very important for dynamically sized cells. A label with number of lines set to 0 will grow based on how much text it is showing. A label with number of lines set to any other number will truncate the text once it’s out of available lines.
Connect the bioLabel outlet of ArtistTableViewCell to the label on the cell. One quick way to do this is to Right-Click the Cell in the Document Outline, then click and drag from the empty circle to the right of bioLabel under the Outlets list in the popup menu to your label that you laid out:


The trick to getting Auto Layout to work on a UITableViewCell is to ensure you have constraints to pin each subview on all sides — that is, each subview should have leading, top, trailing and bottom constraints. Then, the intrinsic height of the subviews will be used to dictate the height of each cell. You’ll do this now.
Note: If you are not familiar with Auto Layout, or would like a refresher to understand how to set up Auto Layout constraints, take a look at this tutorial.
Select the bioLabel and press the Pin button at the bottom of your storyboard. In this menu, simply select the 4 dotted lines towards the top of the menu, change the leading and trailing values to 8, and click Add Constraints. It will look like this


This ensures that no matter how big or small the cell may be, the bio label is always:
0 points from the top and bottom margins
8 points from the leading and trailing margins
Review: Does this satisfy the previous Auto Layout criteria?
Does each subview have constraints that pin all of their sides? Yes.
Are there constraints going from the top to the bottom of the contentView? Yes.
The bioLabel connects to the top and bottom margins by 0 points
So, Auto Layout can now determine the height of the cell!
Awesome, your ArtistTableViewCell is set up! If you build and run the app now, you’ll see that…



nothing has changed. What the what?! No worries, there’s just a bit of code you need to write before the cells can become dynamic.
Configure the Table View
First, you need to configure the table view to properly use your custom cell.
Open ArtistListViewController.swift and replace tableView(_:cellForRowAtIndexPath:) with the following:
func tableView(tableView: UITableView,
cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCellWithIdentifier("Cell",
forIndexPath: indexPath) as! ArtistTableViewCell

let artist = artists[indexPath.row]
cell.bioLabel.text = artist.bio
cell.bioLabel.textColor = UIColor(white: 114/255, alpha: 1)
return cell
}
The code above is pretty straightforward: you dequeue a cell, set its information and a text color, and then return the cell.
Run the app again, and it will look nothing has really changed. You are now using your bioLabel, but it’s just showing one line of text in each cell. Even though the number of lines is set to 0 and your constraints are properly configured so your bioLabel takes up the entire cell, it turns out table views need to be told to let Auto Layout drive the height of each cell.
Back in ArtistListViewController.swift add these two lines of code at the bottom of the viewDidLoad() method:
tableView.rowHeight = UITableViewAutomaticDimension
tableView.estimatedRowHeight = 140
When you set the rowHeight as UITableViewAutomaticDimension, the table view is told to use the Auto Layout constraints and the contents of its cells to determine each cell’s height.
In order for the table view to do this, you must also provide an estimatedRowHeight. In this case, 140 is just an arbitrary value that works well in this particular instance. For your own projects, you should pick a value that better conforms to the type of data that you’ll be displaying.
Build and run, and you should now see all of each artist’s bio :]




Adding Images
It’s nice that you can now read the entire bio of each artist, but there is still more data to show. Each artist has an image and a name. Using these additional pieces of data will make the app look much better.
You will need to add an image view to ArtistTableViewCell, and another label for the artist’s name. Open ArtistTableViewCell.swift and add the following properties:
@IBOutlet weak var nameLabel: UILabel!
@IBOutlet weak var artistImageView: UIImageView!
The image view’s variable name is artistImageView rather than imageView because there is already an imageView property on UITableViewCell.
Open Main.storyboard, select the cell and in the Size Inspector change the Row Height to 140; giving you more room to work with:




Now select the Bio Label’s leading constraint. You can find it in the Document Outline, under the Constraints of the Content View:




Delete this constraint by pressing Delete. Ignore any Auto Layout warnings for now. Grab the left edge of the bio label with your cursor, and drag it to the right so the bio label now only takes up about half of the width of the cell. You will use the left half for the image view and label for the artist’s name:



Drag out a new label, and place it at the bottom of the cell, in the horizontal center of your newly cleared area. Set the text of the label to be Name:




Now Drag out an image view, and place it above the name label:



Finally, connect the outlets for both the new image view and the new label, using the same technique you used for the bio label:




Now it’s time to setup more constraints. Starting with the name label and moving up, use the Pin menu to:
Pin the bottom edge of the name label 0 points from the bottom margin of the content view.
Pin the top edge of the name label 8 points from the bottom of the image view.
Pin the top edge of the image view 0 points from the top margin of the content view.
Pin the leading edge of the image view 0 points from the leading margin of the content view.
Pin the trailing edge of the image view 16 points from the leading edge of the bio label.
With the image view selected, Control-Click and drag to the content view of the cell. Let go, and then select Equal Widths from the menu:



Select this new width constraint from the Document Outline and then change the multiplier to 0.5:





This makes it so the image view’s width will be equal to exactly half of the cell’s width.
There’s just a couple more constraints to add:
Shift-Click both the image view and the name label and choose Equal Width from the Pin Menu
Shift-Click both the image view and the name label and choose Horizontal Centers from the Align Menu
With all of these new constraints, Auto Layout has probably thrown up a few warnings letting you know some of the frames are off. To fix this, select the Content View of the cell in the Document Outline and click on the Resolve Auto Layout Issues menu and choose Update Frames under All Views:



That’s it for the storyboard for now. Open ArtistListViewController.swift and the following 2 lines of code to tableView(_:cellForRowAtIndexPath:), after you set the bioLabel‘s text:
cell.artistImageView.image = artist.image
cell.nameLabel.text = artist.name
And then add these lines after you set the textColor:
cell.nameLabel.backgroundColor = UIColor(red: 1, green: 152 / 255, blue: 0, alpha: 1)
cell.nameLabel.textColor = UIColor.white
cell.nameLabel.textAlignment = .center
cell.selectionStyle = .none
Build and run the app. This screen is now looking much better, but scroll down to Georgia O’Keeffe and you will notice something weird:




The name label is being stretched according to the constraints (its top is 8 points from the bottom of the image view, and its bottom is 0 points from the bottom margin of the cell’s content view).
You can fix this by adjusting a couple constraints. In Main.storyboard select the Name label and create another constraint from its bottom end to the bottom margin of the cell. Now select that constraint from the Document Outline and change its Relation to Greater Than or Equal:



Now select the old bottom constraint of the name label and set its priority to 250:



This way, Auto Layout will break the old constraint if it needs to, since it’s a lower priority than the bottom constraint with the >= 0 relation. Run the app again and everything should be looking great now.
Show me the Art!
If you recall from the beginning, selecting an artist presents a view controller that shows the selected artist’s works. The cells in this table view will need to have dynamic height as well as each work has a different sized image and accompanying data.
The first step, just like before, is to create another subclass of UITableViewCell.
In the project navigator select the Views group and press Command-N to create a new file in this group. Create a new Cocoa Touch Class called WorkTableViewCell and make it a subclass of UITableViewCell.
Open WorkTableViewCell.swift and, like before, delete the two automatically generated methods in WorkTableViewCell and then add these properties:
@IBOutlet weak var workImageView: UIImageView!
@IBOutlet weak var workTitleLabel: UILabel!
@IBOutlet weak var moreInfoTextView: UITextView!
Open Main.storyboard and select the cell in the table view in the Artist Detail View Controller Scene. Set the Custom Class of the cell to WorkTableViewCell and then change the row height to 200 to give yourself plenty of room to work with.


Now drag out an image view, a label, and a text view, and place them as pictured below (the text view is on the very bottom):


Change the text of the text view to “Select For More Info >” and the label to “Name”. Change the mode of the image view to Aspect Fit. Select the text view, in the Attribute Inspector, change its alignment to Centered and disable scrolling:




Disabling scrolling is of similar importance to setting a label to 0 lines. With scrolling disabled, the text view knows to grow its size to fit all of its content, since the user won’t be able to scroll through the text.
A bit further, down past where you disabled scrolling, remove the check from User Interaction Enabled, which will allow touches to pass through the text view and trigger a selection of the cell itself.
Connect the three elements with their corresponding outlets, as you did with the first cell.
Now you’ll add constraints. Starting with the text view and moving up:
Pin the bottom edge of the text view 0 points from the bottom margin of the content view.
Pin the leading and trailing edges of the text view 8 points from the leading and trailing margins of the content view.
Pin the top edge of the text view 8 points from the bottom of the label.
Pin the top edge of the label 8 points from the bottom of the image view.
Center the label by choosing Horizontally in Container in the Align menu.
Select both the name label and image view (using Shift-Click) and choose Equal Widths from the Pin menu.
Pin the top edge of the image view 0 points from the top margin of the content view.
Pin the leading and trailing edges of the image view 8 points from the leading and trailing margins of the content view.
Update the frames like before if Auto Layout displays any warnings. You’re done with the storyboard for now. Just like you had to do with the previous view controller, dynamic cell heights take a bit of code as well.
Open ArtistDetailViewController.swift and replace tableView(_:cellForRowAtIndexPath:) with the following:




This should look very familiar by now. You are dequeuing and casting your cell, getting a reference to the model struct you are going to display, and then configuring your cell before you return it.
Now in viewDidLoad() of the same class, add the following code to the end of the method:
tableView.rowHeight = UITableViewAutomaticDimension
tableView.estimatedRowHeight = 300
This is the same code that you used in the previous view controller as well. Run the app, select Picasso, and you’ll see that you can now browse through an artist’s works:

Not bad, but lets take it to the next level by adding expanding cells to reveal more info about each work. Your client is going to love this!
Expanding Cells
Since your cell heights are driven by Auto Layout constraints and the content of each interface element, expanding the cell should be as simple as adding more text to the text view when the user taps that cell.
Open ArtistDetailViewController.swift and add the following extension:
extension ArtistDetailViewController: UITableViewDelegate {
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
// 1
guard let cell = tableView.cellForRow(at: indexPath) as? WorkTableViewCell else { return }

var work = selectedArtist.works[indexPath.row]

// 2
work.isExpanded = !work.isExpanded
selectedArtist.works[indexPath.row] = work

// 3
cell.moreInfoTextView.text = work.isExpanded ? work.info : moreInfoText
cell.moreInfoTextView.textAlignment = work.isExpanded ? .left : .center

// 4
tableView.beginUpdates()
tableView.endUpdates()

// 5
tableView.scrollToRow(at: indexPath, at: .top, animated: true)
}
}



Here’s what’s happening:
You ask the tableview for a reference to the cell that corresponds to the selected index path and then get the corresponding Work
object.
You change the isExpanded state of the Work object, and then stuff it back into the array (which is necessary because structs are passed by copy).
Next, you alter the text view of the cell, depending on if the work is expanded or not: If it is, you’re setting the text view to display the work’s info property and changing the text alignment to .left. If it isn’t expanded, you’re setting the text back to “Select to See More >” and the alignment back to .center.
The table view needs to refresh the cell heights now. Calling beginUpdates() and endUpdates() will force the table view to refresh the heights in an animated fashion.
Finally, you tell the table view to scroll the selected row to the top of the table view in an animated fashion.
Now in tableView(_:cellForRowAt:), add the following two lines at the end, before you return the cell:
cell.moreInfoTextView.text = work.isExpanded ? work.info : moreInfoText
cell.moreInfoTextView.textAlignment = work.isExpanded ? .left : .center
This code will cause a cell that is being reused to correctly remember if it was previously in the expanded state.
Build and run the app. When you tap a work cell, you’ll see that it expands to accommodate the full text. But the image animates a bit weirdly.




That won’t be hard to fix! Open Main.storyboard and select the image view in your WorkTableViewCell and open the size inspector. Change the Content Hugging Priority and Content Compression Resistance Priority to the values pictured below:



Setting the Vertical Content Hugging Priority to 252 will help the image view hug its content and not get stretched during the animation. Setting the Vertical Compression Resistance Priority to 749 allows the image to be compressed if other interface elements grow around it. But this will only help smooth out the cell expanding animation. The images will not be compressed at all since the cell height will grow if the items inside grow.
Build and run the app. Select an Artist, and then tap on the pieces of work. You should see some very smooth cell expansion, revealing information about each artwork:




Hooray!
Dynamic Type
You’ve shown your progress to your client, and they are loving it! But they have one final request. They want the app to support the Larger Text Accessibility feature. The app needs to adjust to the customer’s preferred reading size.
Introduced in iOS 7, Dynamic Type makes this task easy. Dynamic Type gives developers the ability to specify different text styles for different blocks of text (like Headline or Body), and have that text adjust automatically when the user changes their preferred size in their device Settings.
In ArtistListViewController.swift, add these 2 lines of code to the end of tableView(_:cellForRowAt:), just before you return the cell:
cell.nameLabel.font = UIFont.preferredFont(forTextStyle: .headline)
cell.bioLabel.font = UIFont.preferredFont(forTextStyle: .body)
This is how you set a text based interface element to use Dynamic Type. preferredFont(forTextStyle:) only has one parameter, which is the style you want this text element to use. There are 10 different constants you can use, refer to Apple’s documentation on preferredFont(forTextStyle:) to learn more about them.
Now you just need to make sure the table view refreshes itself whenever the user changes their preferred font size. To do so, add the following method to ArtistListViewController, right below viewDidLoad():





override func viewDidAppear(_ animated: Bool) {
super.viewDidAppear(animated)

NotificationCenter.default.addObserver(forName: .UIContentSizeCategoryDidChange, object: .none, queue: OperationQueue.main) { [weak self] _ in
self?.tableView.reloadData()
}
}
Here you are adding an observer for the onContentSizeCategoryChange: notification, which is fired off whenever the user changes their preferred text size.
The observer uses a closure that tells the table view to reload itself. This will cause tableView(_:cellForRowAtIndexPath:) to be called for all onscreen cells, which will execute the preferredFontForTextStyle(style:) we just added. Now the fonts will always update when that notification is received.
Note:Starting with iOS 9, it is no longer necessary to remove notification center observers. If your app’s deployment target is iOS 8, however, you will still need to do that!
Adding Dynamic Type support to ArtistDetailViewController is nearly identical. Open ArtistDetailViewController.swift and add these 2 lines of code to the end of tableView(_:cellForRowAtIndexPath:):
cell.workTitleLabel.font = UIFont.preferredFontForTextStyle(UIFontTextStyleHeadline)
cell.moreInfoTextView.font = UIFont.preferredFontForTextStyle(UIFontTextStyleFootnote)
Then add the exact same implementation for viewDidAppear(_:) as the previous view controller.




Testing this with iOS 9.3 simulators does not currently work, so you will need to build to your device to try it out. Launch the app on your device, then go to your home screen. Open the Settings app, then tap General > Accessibility > Larger Text, and drag the slider to the right to increase the text size to a large setting:



Then go back to the Artistry app, and your text should now appear larger. And thanks to your work on getting the dynamically sized cells, the table view still looks great:




#Where to Go From Here
Congratulations on completing this tutorial on self-sizing table view cells! :]
You can download the completed project from here.
Table views are perhaps the most fundamental of structured data views in iOS. As your apps get more complex, you’re likely to use all sorts of custom table view cell layouts. Fortunately, Auto Layout and iOS 8 made this task much easier.
If you have any comments or questions, please respond below!

# 到了尾声
恭喜你完成了tableview cells的self-sizing的教程 :]
你可以在这下载完整的项目。
tableview大概是在 iOS 里最基础的视图结构化数据了。随着你的 apps 越来越复杂，你可能会用各种自定义的tableview cell去布局。幸运的是，Auto Layout 与iOS 8 让这些工作变得更容易了。
如果你有任何建议或者疑问，可以在下面留言！
