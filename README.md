📊 StackLineProfileData ImageJ Macro 📈
 
**✨ Description:**  
Extracts intensity profiles along a user-defined line or rectangular selection  
across all slices of an ImageJ stack and compiles the data into a results table  
for easy analysis.
 
**🔍 Overview:**  
This macro gathers intensity values along your selected ROI (line, polyline, or rectangle)  
for every slice in a stack. It creates a table where:  
    - The first column = distance along the selection (x values)  
    - Each other column = intensity profile for one slice (with optional metadata!)
 
  **🚀 Usage:**  
  1️⃣ Open your image stack in ImageJ  
  2️⃣ Draw a line, polyline, or rectangular selection on any slice  
  3️⃣ Run this macro  
  4️⃣ Check out the intensity profiles across all slices in the Results table
 
  **💡 Notes:**  
  - Selection must be a line, polyline, or rectangle — otherwise, the macro exits  
  - Shows a progress bar during processing  
  - Runs in batch mode for faster performance  
  - Export results from the Results window for further analysis

 **Macro Script**
     // StackLineProfileData
    // This ImageJ macro gets the intensity across a line profile of all slices in a stack
    // and writes the data to the Results table, one column per slice.

    // Define a macro named "Stack profile Data"
    macro "Stack profile Data" {
       // Check if the current selection is a line or rectangular selection.
       // selectionType() returns:
       // 0 = Rectangle, 5 = Line, 6 = Polyline
       // If not one of these, exit the macro.
       if (!(selectionType()==0 || selectionType==5 || selectionType==6))
         exit("Line or Rectangle Selection Required");

       // Enable batch mode to suppress image updates for faster processing
       setBatchMode(true);

       // Generate a profile plot for the current selection on the active slice
       run("Plot Profile");

       // Get the x and y values from the generated plot
       Plot.getValues(x, y);

       // Clear the Results table to prepare for new data
       run("Clear Results");

       // Write the x values (usually distance along the selection) to the "x" column
       for (i=0; i<x.length; i++)
           setResult("x", i, x[i]);

       // Close the plot window
       close();

       // Store the total number of slices in the stack
       n = nSlices;

       // Loop over each slice in the stack
       for (slice=1; slice<=n; slice++) {
           // Show a progress bar to indicate processing status
           showProgress(slice, n);

           // Set the current slice
           setSlice(slice);

           // Get the intensity profile of the current selection on this slice
           profile = getProfile();

           // Initialize a label for the column with the slice number
           sliceLabel = toString(slice);

           // Try to read metadata label from the slice (if available), e.g., acquired time or z-position
           sliceData = split(getMetadata("Label"),"\n");

           // If there is metadata, append the first line to the slice label for clarity
           if (sliceData.length>0) {
               line0 = sliceData[0];
               if (lengthOf(sliceLabel) > 0)
                   sliceLabel = sliceLabel+ " ("+ line0 + ")";
           }

           // Store the profile values as a new column in the Results table, one value per row
           for (i=0; i<profile.length; i++)
               setResult(sliceLabel, i, profile[i]);
       }

       // Disable batch mode to update the display
       setBatchMode(false);

       // Refresh the Results window to display the updated table
       updateResults;
    }

 
  **📜 License:**  
  MIT License — free to use and modify 🎉
 
  **👤 Author:**  
  mikaylaeppert
 
