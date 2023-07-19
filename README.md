import android.Manifest
import android.content.pm.PackageManager
import android.graphics.Bitmap
import android.graphics.Canvas
import android.graphics.pdf.PdfDocument
import android.os.Bundle
import android.view.View
import android.widget.Button
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.core.app.ActivityCompat
import androidx.core.content.ContextCompat
import java.io.File
import java.io.FileOutputStream
import java.io.IOException

class MainActivity : AppCompatActivity() {

    private val REQUEST_PERMISSIONS = 1
    private val PERMISSIONS = arrayOf(
        Manifest.permission.WRITE_EXTERNAL_STORAGE,
        Manifest.permission.READ_EXTERNAL_STORAGE
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val createPdfButton = findViewById<Button>(R.id.create_pdf_button)
        createPdfButton.setOnClickListener { createPdf() }
    }

    private fun createPdf() {
        if (!hasPermissions()) {
            ActivityCompat.requestPermissions(this, PERMISSIONS, REQUEST_PERMISSIONS)
            return
        }

        val pdfDocument = PdfDocument()
        val pageInfo = PdfDocument.PageInfo.Builder(300, 600, 1).create()

        val page = pdfDocument.startPage(pageInfo)
        val canvas = page.canvas

        val textPaint = Paint()
        textPaint.textSize = 12f

        val imageBitmap = getBitmapFromView(findViewById(R.id.content_layout))

        // Add image to the PDF
        canvas.drawBitmap(imageBitmap, 10f, 10f, null)

        // Add text to the PDF
        canvas.drawText("Hello, PDF!", 10f, 200f, textPaint)

        pdfDocument.finishPage(page)

        val filePath = File(getExternalFilesDir(null), "example.pdf")
        try {
            pdfDocument.writeTo(FileOutputStream(filePath))
            Toast.makeText(this, "PDF created successfully", Toast.LENGTH_SHORT).show()
        } catch (e: IOException) {
            e.printStackTrace()
            Toast.makeText(this, "Failed to create PDF", Toast.LENGTH_SHORT).show()
        }

        pdfDocument.close()
    }

    private fun hasPermissions(): Boolean {
        for (permission in PERMISSIONS) {
            if (ContextCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
                return false
            }
        }
        return true
    }

    private fun getBitmapFromView(view: View): Bitmap {
        val bitmap = Bitmap.createBitmap(view.width, view.height, Bitmap.Config.ARGB_8888)
        val canvas = Canvas(bitmap)
        view.draw(canvas)
        return bitmap
    }

    override fun onRequestPermissionsResult(requestCode: Int, permissions: Array<String>, grantResults: IntArray) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)
        if (requestCode == REQUEST_PERMISSIONS) {
            var allPermissionsGranted = true
            for (result in grantResults) {
                if (result != PackageManager.PERMISSION_GRANTED) {
                    allPermissionsGranted = false
                    break
                }
            }

            if (allPermissionsGranted) {
                createPdf()
            } else {
                Toast.makeText(this, "Permissions not granted", Toast.LENGTH_SHORT).show()
            }
        }
    }
}
![Screenshot_20230716-221625](https://github.com/zarifmak/zarifmak/assets/139945909/61b4a32e-58d1-4a95-bed4-e67b004f4b40)
![Screenshot_20230716-221632](https://github.com/zarifmak/zarifmak/assets/139945909/801ee6d4-2e96-492e-97ec-fef9a4c9dc90)
![1689659569109](https://github.com/zarifmak/zarifmak/assets/139945909/db1421b8-d0b4-49c5-8937-82fe0e8ccfd9)
