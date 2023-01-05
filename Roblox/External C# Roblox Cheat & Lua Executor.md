Roblox is a really popular game. It's played from all over the world by 
mostly kids that get tired from Fortnite or Minecraft and want to play 
some chill games. As we all know, when playing against other players 
everyone wants to win, right? That's why many people try to use some 
assists to help them out. That's exactly what this external C# Roblox 
cheat is. It's here to help you play better without you actually doing 
anything.


I know many players struggle to find a good Roblox Cheat, so that's why I
 am making this thread to help you out. Not many people want to share 
their hacks, just because they are either selling them or want to keep 
all the exploits private. I've found his amazing external cheat so we'll
 take a look at it. It comes with a fully customizable menu that you can
 customize by just clicking few buttons. You can change menu color, text
 font and font color and even slider color. You can set it up however 
you want so it's easier for you to use it. Now to the features. This 
Roblox Cheat comes with really powerful features such as Lua executor. 
You can write your lua script using Roblox API and it will load it 
inside of the game for you. It's super easy to add new features or use 
someone else's lua script. It also comes with different exploits such as
 name change, godmode and ban player. All of these are working on every 
game server you play, but watch out for ban from admin.


## Sample Code
Lua executor will get a lua script from anywhere on the web and directly
 inject it into the game. Really easy to use and you can use anyone's 
lua script this way. You can also write you own script and execute it.

### Lua executor
```
private void Button1_Click(object sender, EventArgs e)
{
    script.Text = "";
    script.Text = "loadstring(game:HttpGet(('https://raw.githubusercontent.com/Sjorbiia/X-Scripts/master/PrisonXV3.5%20-%20Experimental.lua'),true))()";
    module.ExecuteScript(script.Text);
    script.Text = "";

}
```

You can also load any lua file from your computer. It will simply open a
 Windows prompt and you will be able to navigate where you script is 
located. This way you can have some private exploits only saved on your 
computer and not shared on the web.

### LoadLuaFromDisk
```
private void FlatButton1_Click_2(object sender, EventArgs e)
{
    OpenFileDialog opendialogfile = new OpenFileDialog
    {
        Filter = "Lua File (*.lua)|*.lua|Text File (*.txt)|*.txt",
        FilterIndex = 2,
        RestoreDirectory = true
        };
    if (opendialogfile.ShowDialog() != DialogResult.OK)
        return;
    try
    {
        script.Text = "";
        System.IO.Stream stream;
        if ((stream = opendialogfile.OpenFile()) == null)
            return;
        using (stream)
            this.script.Text = System.IO.File.ReadAllText(opendialogfile.FileName);
    }
    catch (Exception)
    {
        _ = (int)MessageBox.Show("An unexpected error has occured", "OOF!", MessageBoxButtons.OK, MessageBoxIcon.Information);
    }
}
```
