// NAME: 7ssh (collects all passwd and Bank.txt and libs)
// BUG -> create folder if not exist (only if new installed (folder missing))
// BUG: check if rshell script is there

// Change here values if wanted
pathTemp = "/home/" + active_user + "/Downloads" // temporary place till moved to final desination
pathLib = pathTemp + "/libs" // place where all the libs will be stored
pathCracked = pathTemp + "/cracked" // bank and passwd save place
rshellServer = get_router.public_ip

// Checks
cryptools = include_lib("/lib/crypto.so")
if not cryptools then exit("Error: Missing crypto library")
if params.len < 2 or params.len > 3 then exit("<b>Usage: 7ssh [user@password] [ip address] [(opt) port]</b>")
credentials = params[0].split("@")
remoteHost = params[1]
remoteUsr = credentials[0]
remotePas = credentials[1]
port = 22
if params.len == 3 then port = params[2].to_int
if typeof(port) != "number" then exit("Invalid port: " + port)

// FUNCTIONS
GetPassword=function(userPass)
  if userPass.len != 2 then
    return null
  else
	password = cryptools.decipher(userPass[1])
	return password
end function

PrintYellowP=function(string)
  print("<color=yellow>" + string + "</color>")
end function

PrintWhite=function(string)
  print("<color=white>" + string + "</color>")
end function

GetPasswd=function() // crack and store information in a new file
  exportFile = "passwd_" + remoteHost // exp: passwd_12.2.3.1
  exportFilePath = localComputer.File(pathCracked + "/" + exportFile)
  if exportFilePath then exportFilePath.delete // remove in case multiple time run
  localComputer.touch(pathCracked, exportFile)

  PrintWhite("collecting passwd informations ...")
  print("all collected informations will be stored in:\n" + pathCracked + "/" + exportFile)
  print("")
  file = remoteShell.host_computer.File("/etc/passwd")
  listUsers = file.get_content.split("\n")
  content = ""
  for line in listUsers
    userPass = line.split(":")
    password = GetPassword(userPass)
    if password then
      content = localComputer.File(pathCracked + "/" + exportFile).get_content
      localComputer.File(pathCracked + "/" + exportFile).set_content(content + char(10) + userPass[0] + ":" + password)
      print("user: " + userPass[0] + "\npassword: " + password)
    end if
  end for
end function

GetBank=function() // crack and store information in single file
  folder = remoteShell.host_computer.File("/home")
  PrintWhite("collecting bank informations ...")
  if not folder.has_permission("r") then
    print("permission denied: " + folder.name)
  else
    homeFolders = folder.get_folders
    exportFile = "bank_informations"
    exportFilePath = localComputer.File(pathCracked + "/" + exportFile)
    contentBank = ""

    print("all collected informations will be stored in:\n" + pathCracked + "/" + exportFile)
    print("")
    for homeFolder in homeFolders
      file = remoteShell.host_computer.File("/home/" + homeFolder.name + "/Config/Bank.txt")
      if file != null then
        if not file then print("Error: file not found")
        if not file.has_permission("r") then print("Error: can't read. Permission denied.")
        if file.is_binary then print("Error: invalid file found.")

        listUsers = file.get_content.split("\n")
        for line in listUsers
          userPass = line.split(":")
  	    password = GetPassword(userPass)
  	    if password then
            if not exportFilePath then localComputer.touch(pathCracked, exportFile)
            contentBank = localComputer.File(pathCracked + "/" + exportFile).get_content
            localComputer.File(pathCracked + "/" + exportFile).set_content(contentBank + char(10) + userPass[0] + ":" + password)
  	      print("user: " + userPass[0] + "\npassword: " + password + "\n")
  	    end if
        end for
      end if
    end for
  end if
end function

GetLibs=function() // download all libs
  PrintWhite("collecting /lib informations ...")
  folderLib = remoteComputer.File("/lib")
  if not folderLib.has_permission("r") then
    print("permission denied: " + folderLib.name)
  else
    libFiles = folderLib.get_files
    metaxploit = include_lib("/lib/metaxploit.so")
    for libFile in libFiles

      libRemote = remoteComputer.File("/lib/" + libFile.name)
      remoteShell.scp(libRemote.path, pathTemp, localShell) // DOWNLOAD

      metaLib = metaxploit.load(pathTemp + "/" + libFile.name)
      libLocal = localComputer.File(pathTemp + "/" + libFile.name)
      localComputer.create_folder(pathLib, metaLib.version)
      print("file saved: " + pathLib + "/" + metaLib.version + "/" + libFile.name + "\n")
      libLocal.move(pathLib + "/" + metaLib.version, libFile.name)
    end for
  end if
end function

InstallRshell=function() // install and connect rshell on remote
  PrintWhite("connecting to rshellserver: " + rshellServer + " ...\n")
  localShell.scp("/lib/metaxploit.so", "/lib", remoteShell) // UPLOAD
  localShell.scp(pathTemp + "/rshellbackdoor", "/root", remoteShell) // UPLOAD
  remoteShell.launch("/root/rshellbackdoor", rshellServer)
  fileRemove = remoteComputer.File("/root/rshellbackdoor")
  fileRemove.delete
end function

PrintYellowP("running: /bin/7ssh " + remoteUsr + "@" + remotePas + " " + remoteHost + " " + port)

// SSH
print("Connecting to: " + remoteHost + " ...")
localShell = get_shell
localComputer = localShell.host_computer
remoteShell = get_shell.connect_service(remoteHost, port, remoteUsr, remotePas)
remoteComputer = remoteShell.host_computer
if typeof(remoteShell) == "string" then exit(remoteShell)
if not remoteShell then exit("connection failed")

// START function(s) you want to use
//GetPasswd() // only for missions needed
GetBank() // get all bank passwords
//GetLibs() // for lib colleation (needs a lot of diskspace)
InstallRshell() // install rshell-backbone

// CLEANUP: TBD -> check if system.log is there, if not inform user to create an empty system.log
PrintWhite("cleanup /var/system.log ...\n")
localShell.scp(pathTemp + "/system.log", "/var/", remoteShell) // DOWNLOAD

exit("... connection to " + remoteHost + " closed")
