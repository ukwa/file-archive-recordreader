file-archive-recordreader
=========================

File Archive RecordReader

----
Added some very experimental code for ideas for allowing TAR and maybe even ZIP files to be used directly.


The jtar library looks pretty good: http://code.google.com/p/jtar/ 

String tarFile = "c:/test/test.tar";
   String destFolder = "c:/test/myfiles";

   // Create a TarInputStream
   TarInputStream tis = new TarInputStream(new BufferedInputStream(new FileInputStream(tarFile)));
   TarEntry entry;
   while((entry = tis.getNextEntry()) != null) {
      int count;
      byte data[] = new byte[2048];

      FileOutputStream fos = new FileOutputStream(destFolder + "/" + entry.getName());
      BufferedOutputStream dest = new BufferedOutputStream(fos);

      while((count = tis.read(data)) != -1) {
         dest.write(data, 0, count);
      }

      dest.flush();
      dest.close();
   }
   
   tis.close();
   
   
Zip  -built-ins may work

public static void main(String[] args) throws Exception
    {
        FileInputStream fis = new FileInputStream("c:/inas400.zip");

        // this is where you start, with an InputStream containing the bytes from the zip file
        ZipInputStream zis = new ZipInputStream(fis);
        ZipEntry entry;
            // while there are entries I process them
        while ((entry = zis.getNextEntry()) != null)
        {
            System.out.println("entry: " + entry.getName() + ", " + entry.getSize());
                    // consume all the data from this entry
            while (zis.available() > 0)
                zis.read();
                    // I could close the entry, but getNextEntry does it automatically
                    // zis.closeEntry()
        }
    }


Could use Apache Commons Compress, but seems pretty old and rather ugly:
http://commons.apache.org/compress/examples.html

ArchiveInputStream input = new ArchiveStreamFactory()
    .createArchiveInputStream(originalInput);

TAR
---

TarArchiveEntry entry = tarInput.getNextTarEntry();
byte[] content = new byte[entry.getSize()];
LOOP UNTIL entry.getSize() HAS BEEN READ {
    tarInput.read(content, offset, content.length - offset);
}
http://commons.apache.org/compress/apidocs/src-html/org/apache/commons/compress/archivers/tar/TarArchiveInputStream.html

ZIP
---

ZipArchiveEntry entry = zipFile.getEntry(name);
InputStream content = zipFile.getInputStream(entry);
try {
    READ UNTIL content IS EXHAUSTED
} finally {
    content.close();
}
http://commons.apache.org/compress/zip.html