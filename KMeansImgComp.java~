import java.awt.AlphaComposite;
import java.awt.Graphics2D;
import java.awt.RenderingHints;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Random;

import javax.imageio.ImageIO;

public class KMeansImgComp {
    public static void main(String [] args){
		if (args.length < 3){
		    System.out.println("Usage: KMeansImgComp <input-image> <k> <output-image>");
		    return;
		}
		try{
		    BufferedImage originalImage = ImageIO.read(new File(args[0]));
		    int k=Integer.parseInt(args[1]);
		    BufferedImage kmeansJpg = kmeans_helper(originalImage,k);
		    ImageIO.write(kmeansJpg, "jpg", new File(args[2])); 
		    
		}catch(IOException e){
		    System.out.println(e.getMessage());
		}	
    }
    
    private static BufferedImage kmeans_helper(BufferedImage originalImage, int k){
		int w=originalImage.getWidth();
		int h=originalImage.getHeight();
		BufferedImage kmeansImage = new BufferedImage(w,h,originalImage.getType());
		Graphics2D g = kmeansImage.createGraphics();
		g.drawImage(originalImage, 0, 0, w,h , null);
		// Read rgb values from the image
		int[] rgb=new int[w*h];
		int count=0;
		for(int i=0;i<w;i++){
		    for(int j=0;j<h;j++){
			rgb[count++]=kmeansImage.getRGB(i,j);
		    }
		}
		// Call kmeans algorithm: update the rgb values
		kmeans(rgb,k);
	
		// Write the new rgb values to the image
		count=0;
		for(int i=0;i<w;i++){
		    for(int j=0;j<h;j++){
			kmeansImage.setRGB(i,j,rgb[count++]);
		    }
		}
		//System.out.println("DONE!");
		return kmeansImage;
    }

    // Your k-means code goes here
    // Update the array rgb by assigning each entry in the rgb array to its cluster center
    private static void kmeans(int[] rgb, int k){
    	if(rgb.length < k)
    		System.out.println("Not enough pixels, should be greater than K");
    	int[] clusterAssignment = new int[rgb.length];
    	int[] pixelsInCluster = new int[k];   // Number of pixels belonging to a cluster
        int[] clusterTotalAlpha = new int[k]; // Total alpha in a cluster
        int[] clusterTotalRed = new int[k];   // Total red in a cluster
        int[] clusterTotalGreen = new int[k]; // Total green in a cluster
        int[] clusterTotalBlue = new int[k];  // Total blue in a cluster
        
    	int kClusterCenter[] = new int[k];
    	int previousCenters[] = new int[k];
    	
    	//Random cluster center generation
    	ArrayList<Integer> numbers = new ArrayList<Integer>();   
    	Random randomGenerator = new Random();
    	while (numbers.size() < k) {

    	    int random = randomGenerator .nextInt(rgb.length);
    	    if (!numbers.contains(random)) {
    	        numbers.add(random);
    	    }
    	}
    	for(int i=0;i<numbers.size();i++)
    		kClusterCenter[i] = rgb[numbers.get(i)];
    	
    	System.out.println("K value : " + k);
    	System.out.println("Initial cluster centers:");
    	for(int i=0;i<numbers.size();i++){
    		System.out.println(i+1 + " : " + kClusterCenter[i]);
    	}
    	
    	boolean convergeFlag = false;
    	do{
    		for ( int i = 0; i < k; i++ )
    	        previousCenters[i] = kClusterCenter[i];
    	    
    		//prevPixelclusterMap = pixelclusterMap ;
	    	double eucDistance = 0.0, intermediateVal = Double.MAX_VALUE; 
	    	int interIndex = 0;
	    	for(int i=0;i<rgb.length;i++){
	    		intermediateVal = Double.MAX_VALUE;
		    	for(int j=0;j<k;j++){
		    		eucDistance = pixelDist(rgb[i], kClusterCenter[j]);
		    		if(eucDistance < intermediateVal){
		    			intermediateVal = eucDistance;
		    			interIndex = j;
		    		}
		    	}
		    	clusterAssignment[i] = interIndex;
		    	pixelsInCluster[interIndex]++;
		        clusterTotalAlpha[interIndex] += ((rgb[i] & 0xFF000000) >>> 24);
		        clusterTotalRed[interIndex] +=   ((rgb[i] & 0x00FF0000) >>> 16);
		        clusterTotalGreen[interIndex] += ((rgb[i] & 0x0000FF00) >>> 8);
		        clusterTotalBlue[interIndex] +=  ((rgb[i] & 0x000000FF) >>> 0);
		    }
	       
	    	// Update cluster centers to the average of all pixels belonging to cluster.
	        // Average ARGB components individually and then pack into center.
	        for ( int i = 0; i < k; i++ ) {
	          int averageAlpha = (int)((double)clusterTotalAlpha[i] / (double)pixelsInCluster[i]);
	          int averageRed =   (int)((double)clusterTotalRed[i] /   (double)pixelsInCluster[i]);
	          int averageGreen = (int)((double)clusterTotalGreen[i] / (double)pixelsInCluster[i]);
	          int averageBlue =  (int)((double)clusterTotalBlue[i] /  (double)pixelsInCluster[i]);

	          kClusterCenter[i] = ((averageAlpha & 0x000000FF) << 24) |
	                              ((averageRed & 0x000000FF) << 16) |
	                              ((averageGreen & 0x000000FF) << 8) |
	                              ((averageBlue & 0x000000FF) << 0);
	        }
	        if(converged(kClusterCenter, previousCenters)){
	        	convergeFlag = true;
	        	System.out.println("Converged..");
	        }
        }while(!convergeFlag);
    	
    	for(int i=0; i<rgb.length; i++){
    		rgb[i] = kClusterCenter[clusterAssignment[i]];
    	}
    	
    	System.out.println("Final average cluster centers:");
    	for(int i=0;i<k;i++){
    		System.out.println(i+1 + " : " + kClusterCenter[i]);
    	}
    }
    
    private static boolean converged(int[] kClusterCenter, int[] previousCenters){
    	for(int i=0; i<kClusterCenter.length; i++){
    		if(kClusterCenter[i] != previousCenters[i])
    			return false;
    	}
    	return true;
    }
    
    //Euclidian distance calculation for pixel and the cluster center
    private static double pixelDist( int pixA, int pixB ) {
        int aDiff = ((pixA & 0xFF000000) >>> 24) - ((pixB & 0xFF000000) >>> 24);
        int rDiff = ((pixA & 0x00FF0000) >>> 16) - ((pixB & 0x00FF0000) >>> 16);
        int gDiff = ((pixA & 0x0000FF00) >>> 8)  - ((pixB & 0x0000FF00) >>> 8);
        int bDiff = ((pixA & 0x000000FF) >>> 0)  - ((pixB & 0x000000FF) >>> 0);
        return Math.sqrt( aDiff*aDiff + rDiff*rDiff + gDiff*gDiff + bDiff*bDiff );
      }
}
