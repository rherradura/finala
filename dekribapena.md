# deskribapenak:

Honako funtzio hau inplementatu dut ezagutzeko momentu oro zenbat galdera dauden datu-basean eta erabiltzaile horrek egin dituena, honela agertuko da emaitza: Erabiltzaile_galdera_kopurua/Galdera_guztien_kopurua. setInterval funtzioa erabilidi dugu 2 segunduro eguneratzeko zati hori badaezpada beste erabiltzaile batek galdera berri bat gehitu badu gehitzeko galdera.

        setInterval(showQuestionsCount, 2000);
        function showQuestionsCount(){
          xhro2 = new XMLHttpRequest();
          xhro2.onreadystatechange = function(){
          if ((xhro2.readyState==4)){
            document.getElementById("galderaKop").innerHTML= xhro2.responseText;}
          }
          xhro2.open("GET","showUserAndTotalQuestion.php",true);
          xhro2.send();
        }

Modu asinkronoan eginten du, eta php honek lehen aipatutako emaitza hori lortzen du, lortzen alde batetik datu-baseko galdera kopuru totoala eta beste aldetik, erabiltzaile horren galdera kopurua:
		
        <?php
          session_start();
          include 'dbConfig.php';
          $korreo = $_SESSION['email'];

          $mysqli = new mysqli($zerbitzaria,$erabiltzailea,$pasahitza,$db);

          $User = $mysqli->query("SELECT * FROM questions WHERE Korreoa = '$korreo'");
          $Total = $mysqli->query("SELECT * FROM questions");
          echo "<p>Zure galderak: ".$User->num_rows."/Galderak guztira: ".$Total->num_rows."</p>";
          $mysqli->close();

        ?>

----------------------------------------------------------------------------------------------------------------

Bigarren funtzio berria ezabatu funtzioa izan da, non irakaslea bakarrik ezaba ditzazke ikasleak egindako galderak, horretarako, handlingQuizes atalean funtzio hau inplementatu dugu modu asinkronoan:
	
		function ezabatu(){
			var aukera = confirm("Seguru zaude tupla hau ezabatu nahi duzula?");
			if (aukera == true){
				var id = document.getElementById("id").value;
				ID="id="+id;

				xhro.open("POST","deleteQuestionTeacher.php", true);
				xhro.setRequestHeader("Content-type","application/x-www-form-urlencoded");
				xhro.send(ID);
			}
		}

Nahi dugun funtzionalitatea lortzeko, honako php kodigo hau erabiltzen du non lehenik eta behin zihurtatzen da aukeratutako ID galdera hori existitzen dela eta ondoren, exisistitzen bazen, ezabatzen du, bestela arazo mezu bat itzultzen du:

		<?php
			include 'dbConfig.php';
			$id = $_POST['id'];

			$mysqli = new mysqli($zerbitzaria,$erabiltzailea,$pasahitza,$db);
			$konprobaketa = $mysqli->query("SELECT Galdera FROM questions WHERE ID = '$id'");
			if ($konprobaketa->num_rows === 0) {
				echo "Datu basean ez da existitzen ID horrekin tuplarik!"; 	
			}
			$emaitza = $mysqli->query("DELETE FROM questions WHERE ID = '$id'");

			if ($emaitza==TRUE) {
		?>
				<p>Ondo ezabatu da galdera</p>
		<?php
			}else{
				echo "Error: " . $emaitza . "<br>" . $mysqli->error;
			}
			$mysqli->close();		
		 ?>
