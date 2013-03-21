# sStepper
## Lightweight story stepper

Often when creating interactive stories, we need to control different "steps" in our interactive. Often this is a text box with different steps that have information about what the user is seeing. 

To create our stepper, we must first create some basic HTML structure. You want to wrap the whole stepper in an element with the ID **#sStepper**. Inside the sStepper you have the **.stepper-sections**. Optionally, you could also have **#stepper-navigation** if you'd like persistent previous/next buttons.

Any element with the class of **.stepper-next** or **.stepper-prev** will change the stepper in the corresponding direction.

The **#stepper-sections** contains as many elements as you want with the class **.stepper-section**. 
    
### Initializing
    
    sStepper = {}
    sectionCount = 0
    sections = []
    
    sStepper.init = (stepperElement) ->        
    
        sections = $(stepperElement+" .stepper-section")
    

We want to hide all of the sections but the first.

        sections.each((i) ->
            sectionCount++
            $(this).attr("id", "stepper-section-"+ (i+1))
        
            if i is 0
                $(this).addClass("active-step")
            else
                $(this).addClass("hidden-step")
        )
    
### Adding listeners for previous/next    
The user would advance to the next step through either a link in the text, or directional buttons in the stepper. Whatever it is, it needs to have the class of "stepper-next" or "stepper-prev".

    $("#sStepper .stepper-next").click( -> 
        sStepper.nextStep();
    )
    $("#sStepper .stepper-prev").click( ->    
        sStepper.prevStep();
    )

### Checking which section we're on
    
We're gonna want to be able to check on which step we're on, it will have the class **.active-step**.     
    
    sStepper.currentStep = () ->
        activeStep = $(".active-step")

        if activeStep.attr("id") isnt undefined
            stepID = activeStep.attr("id")
            stepNumber = activeStep.attr("id").split("-")[2]        
            parseInt(stepNumber)


### Switching to a section        
We also need to handle users moving to the next and previous steps.
        
    sStepper.switchTo = (targetNum) ->        
        
If the user reaches the last step which we keep track of in **sectionCount**, we want to loop them back around to the beginning.
         
        if targetNum > sectionCount
            targetNum = 1
        else if targetNum <= 0
            targetNum = sectionCount
            
        currentStep = sStepper.currentStep();
        
        #console.log "current", currentStep, "target", targetNum
        
        $("#stepper-section-"+currentStep).removeClass("active-step").addClass("hidden-step")                
        
        newStep = $("#stepper-section-"+targetNum)        
        newStep.addClass("active-step").removeClass("hidden-step")

        
In addition, whenever we change a step, there should be callback functions to exit the old step and enter the new one. That way we could do things like start an animation once a step starts, or change the type of visualization shown on the entire page. We define the callback functions for each section as a data attribute in the HTML.

        exitCallback = $("#stepper-section-"+currentStep).attr("data-exit-callback")
        enterCallback = $("#stepper-section-"+targetNum).attr("data-enter-callback")
        
        if enterCallback isnt undefined
            eval(enterCallback)

        if exitCallback isnt undefined
            eval(exitCallback)

Call the switchTo function for +1 or -1 of the currentStep depending on whether it's the next or previous step.                                
    
    sStepper.nextStep = ->    
        currentStep = sStepper.currentStep();
        sStepper.switchTo(currentStep + 1)            

    sStepper.prevStep = ->
        currentStep = sStepper.currentStep();
        sStepper.switchTo(currentStep - 1) 
        
        
### Navigation        
For navigation we add a link for each section.
    
    for i in [1..$(".stepper-section").length]
        navLink = $("<a href='#' onclick='sStepper.switchTo("+i+")'>Section "+i+" </a> ")
        #navLink.on("click", sStepper.switchTo(i));
        
        $("#stepper-navigation").append(navLink)

            
        
    
    
