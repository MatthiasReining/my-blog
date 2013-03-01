title=How To Copy File Folder In Java
subtitle=some little Java tips
created=2013-03-01T11:25:12
tags=Java, copy, copy directory, copy folder
language=en

Here's some sample code how to copy a file folder in Java. 
You've seen this function 1000times before: now 1001times ;-)

Maybe you like this implementation...

	public class CopyingMachine {

		public void copyDirectory(String sourceDirectoryPath, String targetDirectoryPath) {
			copyDirectory(new File(sourceDirectoryPath), new File(targetDirectoryPath));
		}

		public void copyDirectory(File sourceDir, File targetDir) {
			for (File sourceFile : sourceDir.listFiles()) {
				File targetFile = new File(targetDir.getAbsolutePath() + 
						File.separator + sourceFile.getName());
				
				if (sourceFile.isDirectory()) {
					if (!targetFile.exists()) {
						targetFile.mkdir();
					}
					copyDirectory(sourceFile, targetFile);
				} else {
					copyFile(sourceFile, targetFile);
				}
			}
		}

		public void copyFile(File inputFile, File outputFile) {
			System.out.println("copy " + inputFile.getName() + " to " + 
					outputFile.getAbsolutePath());

			try (FileInputStream fis = new FileInputStream(inputFile);
					FileOutputStream fos = new FileOutputStream(outputFile)) {
				byte[] b = new byte[1024];
				int noOfBytes;
				while ((noOfBytes = fis.read(b)) != -1) {
					fos.write(b, 0, noOfBytes);
				}
			} catch (IOException ex) {
				ex.printStackTrace();
			}
		}
	}


This class is available on [github](https://github.com/mr678/blog/blob/master/src/main/java/com/sourcecoding/blog/business/build/control/CopyingMachine.java).