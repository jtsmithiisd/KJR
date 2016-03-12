# KJR
For Maven dependency on KarelJRobot.jar for the kareltherobot package

The directions for creating a KJR program at http://codenvy.com are below.

The resulting product should look like this: https://codenvy.com/ide-resources/share/project/KarelJRobot/ACompleteProgram

This project is based on Joseph Bergin's Karel J. Robot (https://csis.pace.edu/~bergin/KarelJava2ed/Karel++JavaEdition.html)

This project was inspired by the increasing popularity of Chromebooks in education and the lack of understanding or 
proper consideration of those that make the decisions as to what technology will be used in the classroom.  A Chromebook does not
provide a traditionally proper development environment.  Thanks to newer technologies such as Docker and cloud IDEs this is changing. 
While not yet as robust as a traditional student IDE (I favor bluej myself, http://bluej.org), Codenvy is a workable platform. 
Codenvy is free and offers unlimited projects, which, at the time of this writing (May 2016), appears to be unmatched by any competitor.

This project uses Docker, Maven, and Java.  The Docker and Maven used were learned while creating this project and may not follow 
best practices.  Below are the steps used to create this project in hopes that others may find this information useful in creating
their own projects, particularly if they are unfamiliar with Maven or Docker.

Please credit Jamie T Smith (https://www.linkedin.com/in/jamie-smith-066a63a6) appropriately in any derivative works.

-------------------------------------------------------------------

Create a new project from Maven Java Console in the Samples tab.

Open src/main/java/com.codenvy.example.java/App.java and add the following line below System.out.println("Hello World!");
	javax.swing.JOptionPane.showMessageDialog (null, "Click OK to begin" );
In the upper-right hand corner choose VNC + Java 7 from the pulldown menu, and press the green circle with the white triangle.

In the console, you should see some execution data.  Sometimes Codeenvy fails to create a container, so give it a few gos if it fails.
When it has finished, you should see a link after the word Application near the bottom-center of the screen.  Follow the link.

A browser tab should open with your graphical environment.  If you see the pop up window with the message "Click OK to begin", you
have succeded in getting a Java Swing application to run in a container on a VNC in a browser window.  Go ahead and close the tab.
Also be sure to close the runner with the red circle containing a white square to the left of the Codenvy console or you will use
up your allocation of free gigabyte hours (GBH).

Delete the test directory from src.  Edit pom.xml and remove the <dependency> ... </dependency> tag for junit.

Cut App.java from src/main/java/com.codenvy.example.java/ and paste it into src/main/java/    Delete com.codenvy.example.java
Rename App.java to Tester.java.  Edit the class name in Tester.java from App to Tester and remove package com.codenvy.example.java;
Edit pom.xml from <mainClass>com.codenvy.example.java.App</mainClass> to <mainClass>Tester</mainClass>.  Run it again to make sure
that all changes were correctly made.  Be sure to stop the application after verifying that it still works.

Maven is particular about dependencies on jar files.  An easy way to achieve dependency on an external jar that is not in the Maven
repositories is to find a public link to the desired jar.  I have the necessary jar for Karel J. Robot hosted in a github repository.
Add the following to the pom.xml file before the <dependencies> tag:

  	 <repositories>
    	<repository>
      	<id>com.github.jtsmithiisd.KJR</id>
      	<name>com.github.jtsmithiisd.KarelJRobot</name>
      	<url>https://github.com/jtsmithiisd/KJR/blob/master/KarelJRobot.jar?raw=true</url>
    	</repository>
  	 </repositories>
    
Feel free to supply your own repository.  Also, the <id> and <name> tags don't need to be all that specific.

Now you need to let Maven know that the project depends on the kareltherobot library by adding the following code inside of
the <dependencies> ... </dependencies> tag:

        <dependency>
            <groupId>com.github.jtsmithiisd.KJR</groupId>
            <artifactId>kareltherobot</artifactId>
            <version>1.0</version>
        </dependency>
        
Be sure to use the <id> tag from the <repository> tag as the <groupId> in the <dependency> tag.  
Right-click on External Libraries and choose Update Dependencies.

Create a resources directory in src/main.  This is a standard Maven directory.  Anything placed in here will automatically be
packaged into the jar file.  Create a new file in src/main/resources named Tester.kwld and add the following text to the file:

KarelWorld
streets 10
avenues 10
beepers 1 4 1

Click on the Runners tab towards the bottom of the screen.  Go to the Configs tab and select VNC + Java 7.  
Select the blue COPY button and add the following to the end of the Dockerfile:

RUN unzip -q /home/user/app/application.jar  *.kwld -d /home/user/app/

This will extract all kwld files that were packaged with the jar from the resources directory.  Note that if no kwld files are included,
Docker will fail.  Click the Project default button to ON for this Dockerfile, and set the RAM to 100.  Setting this to
the lowest setting will help conserve students' GBH.  Click on the SAVE button to the right. 

Edit src/main/java/Tester.java to the following:

import kareltherobot.*;
import java.awt.Color;

public class Tester implements Directions {
    public static void main(String[] args) {
      javax.swing.JOptionPane.showMessageDialog (null, "Click OK to begin" );
      
      UrRobot karel = new UrRobot(1, 2, East, 0);
      karel.move();
      karel.move();
      karel.pickBeeper();
      karel.move();
      karel.turnLeft();
      karel.move();
      karel.move();
      karel.putBeeper();
      karel.move();
      karel.turnOff();
    }
  
    static {
        World.reset(); 
        World.readWorld("Tester.kwld"); 
        World.setBeeperColor(Color.magenta);
        World.setStreetColor(Color.blue);
        World.setNeutroniumColor(Color.green.darker());
        World.setDelay(10);  
    	  World.smallView(600);
        World.setVisible(true);
    }
}

Click the green run button.  Click the Application link at the bottom of the page.  Click the OK button in the new tab.
If everything was done correctly, the robot should relocate the beeper.

Be sure to hit the red Stop button when done to conserve your GBH.
