# coding: utf-8
task :default => "build/raw/lelev.raw"

directory "build"

rule ".raw" => [->(f){source_for_raw(f)}, "build"] do |task|
  mkdir_p task.name.pathmap("%d")
  `./txt2raw.pl #{task.source} #{task.name}`
end

task "build/hp42s/lelev.hp42s" => "eVs.txt" do |task|
  mkdir_p task.name.pathmap("%d")
  lines = File.open(task.source).each_line
  File.open(task.name, "w") do |file|
    ["LBL \"LELEV\"",
     lines.count, 1, "NEWMAT",
     "STO \"ELEV\"",
     "INDEX \"ELEV\""].each do |x|
      file.write("#{x}\n")
    end
    lines.rewind
    lines.each do |l|
      [l.strip, "STOEL", "I+"].each do |x|
        file.write("#{x}\n")
      end
    end
    file.write("END")
  end
end

def source_for_raw(raw_file)
  raw_file.pathmap("%{^build/raw/,build/hp42s/}X.hp42s")
end
